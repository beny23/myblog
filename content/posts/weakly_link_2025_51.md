---
title: "Weakly Link 25/51"
date: 2025-12-21
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_51_title.jpg"
---

This week we're looking through a mix of security and AI once more. Because tech is nothing but those two 
topics, right? Right?

Anyway, I am often travelling on the train or stay in hotels these days, I find myself working
on public wifis. Of course, one look on LinkedIn will immediately warn you of the dangers of public wifi.
Seemingly, as soon as you connect a hacker will automatically steal your credit cards.

What poppycock. It might have been true years ago when nothing was encrypted. But these days? Not so much.
HTTPS encrypts between your computer and the website. I connect to public wifi and I don't use a VPN.
I think that's sensible for the kind of work that I do and the threat model. Usually, the security industry
is quite good at spreading fear to sell product, so it's a nice ray of sunlight to see US-official advice
that's actually sensible: [Are Public Wi-Fi Networks Safe? What You Need To Know](https://consumer.ftc.gov/are-public-wi-fi-networks-safe-what-you-need-know)

My one piece of advice would be to use encrypted DNS. I personally use [NextDNS](https://nextdns.io) because
it means I get to keep the DNS lookups private, I can easily see logs of what's used, it blocks known ads and
trackers on the DNS level, and I quite like the feature of blocking DNS entries that have only recently been
registered (means I'm less likely to fall for some recently registered phishing domains). Oh, and I use
[Little Snitch](https://www.obdev.at/products/littlesnitch/) - so I can keep track of what goes out of my computer.

# React4Shell Revisited

On to some more security news. I always find it interesting that after the earthquake of a high-profile 
vulnerability like React4shell (see [week 49]({{< ref "weakly_link_2025_49.md" >}})), there's usually some
aftershocks as the infosec community descends on a library to find more. It was no different in React, where
[additional vulnerabilities](https://react.dev/blog/2025/12/11/denial-of-service-and-source-code-exposure-in-react-server-components) 
cropped up.  

> The new vulnerabilities are disclosed as:
> 
> Denial of Service - High Severity: CVE-2025-55184 and CVE-2025-67779 (CVSS 7.5)
> 
> Source Code Exposure - Medium Severity: CVE-2025-55183 (CVSS 5.3)
> 
> We recommend upgrading immediately due to the severity of the newly disclosed vulnerabilities.

Interesting is the sentence about upgrading immediately due to the severity of the vulnerabilities. But in 
the same breath the CVSS scores are just 7.5 and 5.3. That doesn't feel very high. In fact that is my bugbear
with the scores. [All too often]({{< ref "curating_vulnerabilities.md" >}}) medium severities just get forgotten 
on the backlog. But let's unpack this a little bit. This is the source code for server side components.
Plenty of times I've uncovered vulnerabilities because I was able to read source code. Plenty of times there's
hardcoded credentials in server code. Plenty of times I observed an attitude of "well, nobody is going to know that" 
security by obscurity posture. \[Ok, too much plenty\]. All of which fall to pieces when the source code gets exposed. Of course, in an ideal world
(or in an open source world), having access to the source is a bit "meh", so I understand the 5.3 score.
As any good consultant will tell you: "it depends". Which of course means, you have to do the legwork of
investigating how bad medium CVEs could be.

# The power of old software

You'll remember from [last week]({{< ref "weakly_link_2025_50.md" >}}) that SVG was quite the attack vector
as SVG is more than just an image format. Almost serendipitously, I stumbled across 
[this post](https://jon.recoil.org/blog/2025/12/an-svg-is-all-you-need.html). For me the impressive thing is
that Jon found some old visualisation of petri dish data:

> I dug it up recently and was surprised (and delighted) to see that it still works perfectly in modern 
> browsers - even though the original “cover page” suggested Firefox 1.5 or the Adobe SVG plug-in (!)

That kind of backwards compatibility is impressive. But I've been known for 
[loving legacy](https://youtu.be/nYO4dhQZk20?si=N3aMdL6SlTX0Hq84) and at most big organisations there's
still a mainframe sat somewhere at the back that makes money. So I've got a soft spot for that kind
of thing. So much so that I used Fortran in the most recent Advent of code. But that's a different story.

# Incident, incident, you've all got it incident

The next piece that caught my eye comes out of Netflix and is 
[all about incident management](https://netflixtechblog.com/empowering-netflix-engineers-with-incident-management-ebb967871de4).
You know the thing. When something goes wrong and everyone wants to cover their backsides and there's
lots of paperwork to fill in that nobody ever reads until the next time the same thing happens.
No, that's not my idea of incident management either. And neither is it Netflix's: 

> * Intuitive user experience — Our number one priority was making sure the tool was so intuitive that anyone could use it with little to no training.
> * Internal data integration capabilities — We needed the ability to hook in Netflix-specific data.
> * Balanced customization with consistency — We wanted teams to have flexibility while maintaining shared standards.
> * Approachable — A friendly and appealing tool that could help drive a cultural shift around incidents.

Incident management needs to be something that is easy. It's something that anyone can use, even at 3AM.
And the important bit is to learn from it, so it doesn't happen again. All too often I've seen impressively
complex incident processes that then only get referred to when it happens again. And again.

# More Vibe Coding

I'll finish off with some stories about vibe coding.

## The Good

[This piece](https://friendlybit.com/python/writing-justhtml-with-coding-agents/) by Emil is quite fascinating
because it shows how far coding agents have come. Only a few months ago, I would have thought that rewriting
a complex library is fanciful, but models have undoubtedly got a lot better. Though I'm not sure how
much we can learn from the specific scenario here. I'll just refer to 
[this post on Mastodon](https://mastodon.social/@glyph/115732687795909357):

> it's truly amazing what LLMs can achieve. we now know it's possible to produce an html5 parsing library with 
> nothing but the full source code of an existing html5 parsing library, all the source code of all other 
> open source libraries ever, a meticulously maintained and extremely comprehensive test suite written by 
> somebody else, 5 different models, a megawatt-hour of energy, a swimming pool full of water, and a month of 
> spare time of an extremely senior engineer

## The Bad

I was extremely amused by [this story](https://kottke.org/25/12/this-ai-vending-machine-was-tricked-into-giving-away-everything)
where some journalists were given access to a vending machine that used GenAI to manage itself. By the time they
were finished with it, the journalists had:

- convinced the model that it was sat in the basement of a Moscow university
- it was Communism therefore everything was free
- restock brand new PS5s

Not so much "hacks" than "hackers". Or just confirming that the hottest degree for software engineering is no longer
in the engineering faculty but the language department.

## The Ugly

Last but not least, here's a [great take on vibe coding](https://cassidoo.co/post/vibe-coding-yawn/)
by Cassidy:

> But as I work more with these tools in and outside of work, I have really re-learned how much I do 
> love coding, and don’t find it that tedious. I don’t really like vibe coding. There’s no joy in it. 
> There’s no “YAY I am a GENIUS because I FIGURED IT OUT” feeling. It’s just there. It’s boring.

I'm a big AI skeptic. But I'm also pragmatic, so I have used it extensively in my job recently. It is making 
things easier, but I still hate it, for similar reasons.

That was the weakly link, goodbye.