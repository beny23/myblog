---
title: "AppSec loves Agile"
date: 2023-10-18
tags: ["agile","security","appsec"]
featured_image: "/images/appsec_loves_agile_title.png"
---

I describe myself as an Agile Fundamentalist because I really like the ideas of the Agile manifesto, and I also 
confidently state that I am an AppSec snooper, because in my day job I tend to look at other people's code, logs
and systems and break them. I happen to think that agile and application security go together rather well!

I had two different Slack conversations recently. One was on the UK cross-governmental `#security` Slack channel and 
the other one was on the Equal Experts `#securit-ee` Slack channel (yes I'm a consultant, and our Slack channels have
hilariously got `ee` in their names). 

I wonder whether you can tell which is government and which is private sector:

# Conversation A

Me:
> Hello, I was wondering whether I could garner some opinions. Personally, I think that Agile and AppSec go hand in 
> hand well, particularly at scale, but I'd love to hear from people that disagree with me there (or agree)

Them:
> By in large, I suspect you’ll find yourself in an echo chamber in this forum. if you can find someone that is willing 
> to stand up publicly and say they disagree it’d be a really fascinating debate to run!

# Conversation B

Me: (same as before)

Them:
> Ooooh, it would be nice but I think you'll find that a lot of places really don't think that agile and security go 
> together well, and will baulk at developers automating scheme changes and having access to production logs for fear 
> of bad actors but think nothing of giving the DBAs full control over the database.

It did surprise me a little bit that the resistance to doing security in an agile way was more coming from the private
sector rather than the government. In fact, [MBS](https://twitter.com/bruntonspall) now in the cabinet office co-wrote a book about 
[Agile Application Security](https://www.amazon.co.uk/Agile-Application-Security-Laura-Bell/dp/1491938846).

Then again, it should not surprise me that agile security (or DevSecOps to give it its proper buzzword) feels a bit 
strange. I'm not sure whether you'll have read [that McKinsey article](https://www.mckinsey.com/industries/technology-media-and-telecommunications/our-insights/yes-you-can-measure-software-developer-productivity)
which got ripped to shreds rather well [here](https://tidyfirst.substack.com/p/measuring-developer-productivity), 
[here](https://www.youtube.com/watch?v=yuUBZ1pByzM) and especially [here](https://dannorth.net/mckinsey-review/) but for
me the biggest trigger was this diagram:

![McKinsey outer loop](/images/appsec_loves_agile_mckinsey_outer_loop.png)

McKinsey was basically saying that security is an afterthought and developers should minimise spending time on it. Yes,
that's going to end well...

# Why can't someone else fix security for me?

So I have been on both sides, I've been a developer for over 20 years, and I've been in security for the last few years.
Still, I have never really understood the attitude that groans and moans about security.

Why is it an issue? Well, usually because security checking is left to the last minute all too often.
How many times have you worked in a place that calls itself agile, but works in big releases. Where security is left to
a tickbox exercise and pentesters are brought in 2 weeks before the release date to make sure that the blame can
be pointed at someone else when things go tits-up.

If agile has taught us anything, we should know that iterating and fast feedback loops are crucial when it comes to
delivering software. I'll just point to [Dave Farley's YouTube channel](https://www.youtube.com/@ContinuousDelivery) 
as he's much better at explaining why this is important.

So why should security be any different? No tool or vendor is going to come in a magically "fix security". No buzzword
or process is going "solve security". 

# Wait, you mentioned agile!

So let's have a look at the [agile manifesto](https://agilemanifesto.org):

* Individuals and interactions over processes and tools
* Working software over comprehensive documentation
* Customer collaboration over contract negotiation
* Responding to change over following a plan

This to me is agile. Not Scrum, not Kanban, not (god forbid) SAFe. Work from the basic principles and find what works
in your context.

# How does it apply to AppSec?

If I was to write an agile AppSec manifesto, what would that look like? I'd probably just start with the agile manifesto
and iterate, geddit?

## Individuals and interactions over processes and tools

I think this one is straightforward. If your security relies on long check lists or tools that evaluate vulnerabilities,
then I think you are going to drown. Particularly at scale.

My go-to example are supply chain vulnerabilities, where vulnerabilities are now getting published at a pace of [78 new
ones a day](https://jerrygamblin.com/2023/07/03/2023-first-half-cve-data-review/). We're also reaping the whirlwind
of CVEs getting raised to [gain notoriety](https://github.com/FasterXML/jackson-databind/issues/3972#issuecomment-1596193098) 
or getting [badly misclassified](https://daniel.haxx.se/blog/2023/08/26/cve-2020-19909-is-everything-that-is-wrong-with-cves/).
How do we expect developers to stay on top of this? Usually teams are under pressure to deliver new features and fix
bugs, not deal with a tsunami of dependency updates.

Now, security software vendors love to come into this space and sell fear. If you don't patch everything with CVSS score
of 8 or higher within 48 hours, it's doom. And that is - politely put - [bollocks](https://www.theregister.com/2021/02/18/cve_exploitation_2_6pc_kenna_security/).

My own take is that an AppSec team should work hand in hand with developers, [curate vulnerabilities]({{< ref "curating_vulnerabilities.md" >}})
and that automatic dependency updates are [not always desirable]({{< ref "automatic_dependency_updates.md" >}}). If
developers were to follow processes and not leave a single CVE unpatched, I believe fatigue would set in very very 
quickly and soon nobody would look at the reports anymore. When I was a dev, I remember when I first added the 
[OWASP dependency checker](https://owasp.org/www-project-dependency-check/) to a build pipeline. Very quickly
we switched it to reporting only as the pipeline was failing all the time. And then nobody looked at the reports for months.

## Secure software over security checklists

One of my biggest peeves is when security and software development do not go hand in hand. I forget where I saw the quote,
but "security is just QA" is an important piece of the puzzle. And a good tester will always want to understand the
software that needs to be tested. Personally, I despair when external pentesters are engaged for usually a lot of money
and because they are lacking context on what the system under test is doing, they fire off Metasploit and generate a report
about missing HTTP headers and out-of-date TLS ciphers, and miss the fact that an API can be used to access everyone's
payment data.

I also despair when there are AppSec engineers that don't speak the language used to implement the software.

Good AppSec needs help the people who develop the software. It is all about context and collaboration:

![AppSec ouroboros](/images/appsec_loves_agile_ouroboros.jpg)

AppSec engineers on their own will not secure a system. Developers on their own will not respond to all the security
challenges. But together, there is a chance! Security champions or security community of practice initiative can 
be a great way of spreading security skills and sharing the vulnerability analysis load. Lisi Hocke's 
[AskAppSec post on Security Champions](https://www.lisihocke.com/2023/10/askappsec-security-champions.html) is a great 
place to start.

## Collaboration over Policies

I don't like the blame game or finger-pointing. It makes no difference whose fault a security breach is. Fact of the
matter is, security incidents will happen. Things go wrong, and attackers will succeed. I'd much rather help developers
who are under a strict timeline to make compromises, rather than demanding perfect security. I'd much rather be open
and approachable than a strict security gatekeeper.

## Responding to Change over Perfect Security

Every security vulnerability is a risk. And not every risk is something that needs to be fixed straightaway or
even at all. I think AppSec needs to be pragmatic and work with the developers and "the business" to ensure that 
software is as secure as it needs to be. I don't think that every software needs hardware keys and quantum-resistant 
cryptography for everything (not yet anyway). 

# Conclusion

Security is hard. It also is expensive, and from the outside provides no value. Security only becomes important in 
hindsight, when that breach happened or when an embarrassing leak occurred. Going back to the McKinsey diagram,
the only way to keep on top of it is to embed security into the development processes, not keep it away. As AppSec engineer, I much
rather find a problem before it goes to production, rather than finding out about it when the brown stuff has already hit
the spinny stuff. That means reaching out to developers and helping them write secure software and finding problems. 
I think that's where AppSec can learn a lot from the agile manifesto. I hope you found this rant useful.

![People over process](/images/appsec_loves_agile_people_over_process.jpg)



