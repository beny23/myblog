---
title: "Harvesting Logs for Fun and Profit"
date: 2023-03-23
tags: ["appsec","security","logs","scale"]
featured_image: "/images/harvesting_logs_for_fun_and_profit_title.jpg"
---

From a security point of view, application logs are two-sided. On the one hand, it is really important to have good
observability, to find out what is happening and what has happened. On the other hand, we don't want to leak
sensitive information. In this post I am going to look at the kinds of things you might find in your logs.  The juicy
bits are Personal Identifying Information (PII) or security credentials. This gets more and more difficult when
operating at scale.

![PII Flow](/images/harvesting_logs_for_fun_and_profit_overview.png)

In any suitably large organisation, the number of services or log streams are going to be much bigger than
is possible for anyone to keep in their heads. This makes it really important to have good logging infrastructure.
I don't want to be able to have to ssh into individual instances to be able to search log files. Fortunately, there
are a number of solutions that allow the logs to be shipped to a central place and then searched. A popular approach
is the [ELK stack](https://www.elastic.co/what-is/elk-stack) which is ElasticSearch (storage, indexing and searching),
Logstash (shipping the logs from services) and Kibana (User interface). Other approaches are available, but for the
purposes of this post, when I refer to Kibana, I use it interchangeably with ELK or other logging solutions.

# What is PII?

As I stated before, PII stands for Personal Identifiable Information.  Probably the canonical source of the definition 
is by the Information Commissioners Office (ICO): [Personal Data](https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/key-definitions/what-is-personal-data/), 
see also [Guide to the GDPR](https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/).

The way I interpret it (big caveat, I'm not a lawyer):
- Information that can uniquely identify a person - DON'T LOG
- Don't put a name in logs.
- A single piece of information (like a National Insurance Number - NINO) - not great, but not necessarily disallowed.
- NINOs are funny beasts, they're classed as PII as a person can be indirectly identified using additional information, 
  but on their own, are pretty useless.  Also, there is no such thing as a valid test NINO, i.e.  AA123456A is a valid NINO too.
- Unique Taxpayer Reference (UTR) - it's quite likely that any random sequence of 10 digits is a valid UTR - same 
  treatment as NINOs, but on its own, it doesn't tell me much.
- Leaking an email address on its own is worse than a NINO, as that can be used to phish people - because really it's 
  an email address and the fact that it's a valid email address and usually contains a person's name. DON'T LOG!
- Leaking bank account details on its own is worse than a NINO, as it could lead to financial losses - DON'T LOG!
- Putting two (or more) bits of information is worse than one.  If a NINO and Name is leaked, I could build up a 
  picture of NINO and name associations - if I find an email address too, it could enable social engineering attacks - DON'T LOG!

# Why does it matter if we log PII?

You might ask why should I care whether someone's name, email address, phone number, bank details, bank statements, 
national insurance number and tax details is logged into Kibana?  You've got to log in, right?

Well, usually not everyone with access to Kibana is allowed to look at PII (often requires security clearance). But
the solution is NOT to restrict logs. It is immensely useful for lots of engineers to have the ability to look at logs:

- Restricting production log access to only a few people in an Ops team just means they become bottlenecks.
- Restricting log access to only engineers that look after the particular service means nobody else is in a position
  to diagnose issues and find faults.
- With more agile approaches such as [You Build It, You Run It](https://you-build-it-you-run-it.playbook.ee/what-is-you-build-it-you-run-it),
  you want and need to open up logs to more and more engineers. Certainly I found it a prerequisite to [making software
  quickly]({{< ref "making_software_quickly.md" >}})
- If logs contain sensitive information, holding the logs becomes more complex and we need to start thinking about
  auditing access and encrypting
- And the same for the backups of the logs 

When operating at scale, there are probably more than 1,000 people with Kibana access.  What would happen if an 
engineer went rogue or got blackmailed into using their access? 

# Is it just PII that You Worry about?

No, leaking API authorisation tokens or session cookies is just as bad as it could theoretically be used to 
impersonate other systems and users and get access to their data that way.

# Enough with the Fear Mongering, You Mentioned Profit!

The difficulty of finding PII in logs is two-fold:

- In Kibana "words" are indexed, typically all search fields are internally represented as sets of words - 
  and sometimes not intuitively so, If there is a term `host.name` in a log message, searching for `host` will not find 
  it because `.` is classed part of a word...
- Not all leaks are nicely labelled. Searching for `"email"` will not find all the occurrences of email addresses.

# Tips for searching

- Make sure you select the right index (this will increase the performance as it will cut down the search space)

![Select the right log index](/images/harvesting_logs_for_fun_and_profit_right_index.png)

- Wildcards work a bit unexpectedly
  - `message:test*` works (finds any messages that have WORDS starting with test) but `message:"test*"` doesn't 
     (will try to find words that do contain the `*`)
- Use the `.raw` suffix
  - `app:email` will find both `email` and `email-renderer` whereas `app.raw:email` will just find `email`
  - the `.raw` fields will just work on the whole string
- Use regular expressions
  - Yes they work! Use `message:/[A-Z]{2}[0-9]{6}[A-Z]/` to find any message that has a NINO in it... the crucial bit 
    is to remember that these work on WORDS - not whole fields
- You can use regexes on `.raw` fields but they tend to be slower
- Don't restrict your search to just the message field
  - Sometimes leaks happen in different fields
  - Sometimes apps write out non-JSON log lines (e.g. using `println` instead of a logger which is still captured in the `log` field
- Be terse when filtering out terms
  - Consider using kibana filters rather than really long search strings
  - `NOT message:foo AND NOT message:bar AND NOT message:baz` can be rewritten as `NOT message:(foo OR bar OR baz)` 

# My favourite search terms

As I mentioned above, not all leaks are nicely labelled, so it is easy to miss leaks into kibana. The following are
some ways of still finding interesting entries without relying on labels:

## Email addresses

The easiest way to search for email addresses is to search for "gmail.com" - there are so many email addresses, and 
as `@` separates words, I can just search for it.  Admittedly, not all occurrences of "gmail.com" are leaks, but by 
filtering out false positives, it is quite easy to get there. 

Once I found an occurrence, it is quite straightforward to craft other kibana queries that will find all kinds of
email addresses, rather than just Google ones.

## Bank account details

I found that the best way of finding bank account details (in the UK) is to search for sort codes. But how do we search 
for sort codes? Searching for a six-digit number will bring up lots and lots of things that aren't sortcodes.  So we 
just have to be sneaky. It so happened that the client had an endpoint that was used to retrieve metadata for sort codes
which had the sort code as part of the URL. So I can search for all the metadata occurrences in the nginx logs and 
created a visualisation:

![Sort code frequency](/images/harvesting_logs_for_fun_and_profit_sort_codes.png)

The above told me that `090128` is the most frequently used sort code, so let's just search for that...

## Address

Searching for an address is tricky.  

- If we have services like an address-lookup that allow searching for by postcode this is public information
- Still, by filtering out search results of addresses and searching for "Street" I have previously found some PII leaks - 
  though I had to exclude a fair few services and terms

## Authorisation Tokens

Tokens are good fun to search for.  

- Searching for `Authorization AND Bearer` is a good start, but it tends to bring back a lot of error messages that 
  include the authorisation header but obfuscate the value.
- Looking for Bearer AND `/[A-Z0-9]{32}/` can be easier (assuming the bearer token is a 32-character string)

## Based-64 encoded strings

- Searching for `eyJ*` will return base64 encoded JSON - as this is the base64 representation of `{"` which will 
  typically lead to some interesting JSON. Because base64 data is not immediately visible in the logs, it often
  gets left and can be an interesting source of PII.
- Searching for long strings like `VGhpcyBpcyBhIHNlY3JldCBwYXNzd29yZCBhbmQgaXQgc2hh` can sometimes lead to
  sensitive data. This can be done by searching for '/[A-Z0-9]{50,}/' (any string A-Z and 0-9 that's 50 characters
  long or over). I'd previously found base64 encoded images containing scans of passports that way.

# So now we can find it, how can we guard against it?

Here are a few recommendations:
- Think twice before logging any payloads.  Do you really need the payload, could the payload contain PII now or 
  in the future?
- Think twice before enabling DEBUG logs in production. 
- Think twice before logging all the HTTP headers
  - The Authorization header could give you access to an API
  - The session cookie could allow someone to impersonate a user
- Think twice before using `println` to debug a service locally, I've seen these accidentally committed, PR approved 
  and then deployed to production for months - all the while leaking lots of PII.  But these as records sometimes don't 
  appear in the message field and often go unnoticed. Consider code linting rules to protect you from these going 
  live by mistake.

# Dammit, I've found something! What do I do?

First (and most importantly): it happens! It happens to the best of us and is just something that we all go through.  
Mistakes happen, and it's on us to learn from them, so don't beat yourself up about it. Second, don't panic, hopefully
you have a good development process allowing you to make changes quickly. Find and fix the problem. Collaborate
with whoever looks after your logs to purge the offending PII. Have a blameless postmortem.

# You still haven't explained profit?

I could mention that there are plenty of ElasticSearch instances on the public internet, but that is a bit too 
nefarious. Still, it drives an important point. Even if your logging data is accidentally
exposed to the wrong people, having good logging hygiene de-risks the fall-out.