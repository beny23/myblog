---
title: "Weakly Link 26/01"
date: 2026-01-03
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_01_title.jpg"
---

Like any blog post at New Year's, we'll be looking to the future. We'll also check our assumptions
and we've got some security content before looking at a website to destroy all websites.

I did like [this piece](https://www.theregister.com/2025/12/29/four_tech_trends_2025/) on The Register which looks
at four potentially game changing technologies without mentioning AI.

> The oxygen of publicity this year has mostly been consumed by our two-lettered friend, AI. 
> There's no reason to think this will change in 2026. However, through the magic of journalism, 
> here's a world where that's not true, a world where other things are happening that will shape the 
> future. We like to call it the real world, and here's what's happening there and why it matters.

The interesting bit is I hadn't even heard of Structural Battery Composites or a Cybersecurity Mesh
before, but both could be rather interesting. The other two were progress in small nuclear reactors
and accessibility. Well worth a read.

# Data Centres in Space are Stupid

You've probably heard some billionaires talking about how moving compute into orbit is going to be the
best thing since sliced bread. I rather liked [this brutal takedown](https://blog.dshr.org/2025/12/data-centers-in-spaaaace.html)
by David. 

> So the largest satellites ever can power 14% of a current Nvidia rack. The next generation that requires 
> Starship to launch can power 70% of a current Nvidia rack. By the time they can launch it, it will power 
> 17% of a contemporary Nvidia rack. See, progress!

And

> Thus to keep one 2025 data center operating in space they need to launch 2000 enormous satellites per year, 
> or over 5 per day. It is true that solar power in space is free, but launches aren't.

And those two weren't even the biggest issue.

Maybe it is better to keep your feet and data centres on the ground...

# Challenge your assumptions

Over the course of an evening over the holidays I quite enjoyed playing [this game](https://upgrader.gapminder.org)
which looks at what different countries do and several UN-led programmes. It's easy to think that things have 
always got worse. But over the course of about a century the global life expectancy has doubled, education
and healthcare has got a lot better. It is really easy to be depressed about the world these days,
so sometimes it is rather good to have a reminder that not everything is doom and gloom.

# Streisanding Vulnerability Disclosure

Ross found some nice [vulnerabilities in Eurostar's chatbot](https://www.pentestpartners.com/security-blog/eurostar-ai-vulnerability-when-a-chatbot-goes-off-the-rails/)
but the reason this is interesting is the rather unfortunate way the vulnerability disclosures were dealt
with. The pattern was:

1. Ignore
2. Ignore
3. Ignore
4. This is blackmail!

Rather unfortunate when a vulnerability disclosure programme exists. This is particularly unfortunate because
of course, appearing reluctant to acknowledge vulnerability reports and insinuating that it is blackmail
only achieves the opposite and gives the story much much more airtime (see [Streisand effect](https://en.wikipedia.org/wiki/Streisand_effect)). 
My advice to companies: make sure your vulnerability reports are read and acknowledged. I have much more 
trust in companies that are open about the vulnerabilities that were found rather than the ones that suppress 
publications. Because, well, what else have they got to hide?

# Free from FreeBSD jail

I really liked the presentation that Ilja and Michael gave at 39c3 about 
[escaping FreeBSD jails](https://media.ccc.de/v/39c3-escaping-containment-a-security-analysis-of-freebsd-jails).
FreeBSD is often held up as a more stable and secure platform. Certainly a lot of Firewalls run on it.
Now, I'm not saying that FreeBSD is insecure, far from it. And Ilja also mentioned how good the FreeBSD
people were in terms of comms and interacting once notified of the vulnerability. But there's an interesting
observation in there. All too often, critical code comes from a different age: A lot of code was written
before containerisation or jails had been thought of. A lot of the time code works under the assumption,
oh well, only root can run it.

I found the video hugely enjoyable as it gives a great window into kernel hacking without having to
understand kernel hacking. Sure a fair number of things went over my head, but the basic process of:

1. Jailed process builds kernel module
2. Jailed process manages to inject custom kernel module
3. Kernel module unjails the process (and the shell that calls it)
4. Tidy up
5. Cigars

Makes a lot of sense. Ilja called it too easy to exploit. And he called for the death of C. It would be
lovely to have a memory safe kernel. But I think this is going to be a generational project because there's
just so much out there.

# Old stuff is good stuff too

After the admonishment of old software, I'd like to make a counterpoint too. Sometimes, having old, reliable
friends to do carry out well-understood jobs is rather advantageous. I very much liked Frederico's write
up about [using squid to provide egress protection to kubernetes](https://interlaye.red/kubernetes_002degress_002dsquid.html).

> I used Squid deliberately because it predates Kubernetes and most “cloud-native” tooling, but still does 
> exactly what this problem requires: explicit HTTP/HTTPS egress control, logging, and policy enforcement. 
> The point here isn’t to be original, but to show that older, well-understood components still fit naturally 
> inside Kubernetes when used intentionally. Squid has a very good feature set around access control and 
> visibility, and is much less “ingress-first” than common alternatives.

Bravo. Not everything has to be fixed by a service mesh! Come to think of it, not everything needs
a k8s cluster, but that's a different conversation.

# Keep Secrets out of Logs

Next up - and this is not a recent post, and I've not seen it before, but I think it deserves a mention:
Allen walks us through how to [keep secrets out of logs](https://allan.reyes.sh/posts/keeping-secrets-out-of-logs/).
I had to laugh out loud quite a few times and I got nechache from the nodding along. I think especially
in big organisations with lots of staff turnover and lots of 3rd party contractors, it is quite essential to
have good log hygiene. Yes, you want to strike a fine balance between having the right information to diagnose
problems, and logging critical credentials so that anyone of 1000s of engineers who can access the observability 
stack can walk away with the crown jewels.

> You have somewhere between 5 and 50 other problems in your backlog that seem more important, 1 of which 
> you found out about this morning. But I think it’s likely that none of those problems are nearly as annoying. 
> While researching this topic, I interviewed about a dozen other engineers and, on this point, they 
> unanimously agreed! Nobody likes dealing with secrets in logs because it is extraordinarily annoying.

Shameless plug: I've previously written about [finding gold in logs]({{< ref "harvesting_logs_for_fun_and_profit.md" >}})
and it is really quite tricky to get them out AND keep them out. So the idea of designing application code
to make it impossible to leak secrets into logs chimes well with me. Though I also think it's something that is
really tricky to fix in code as it would probably require a massive re-write. Hmmm, seems like a similar 
problem to memory safety above. Unfortunately, just writing afresh is not going to be feasible.

# Writing. Afresh.

Lastly this week, I liked the call by Henry to change how content gets created. All too often, we use
platforms to create our content, and then live or die by that platform. I think that keeping hold of your
own content and then using the platforms to syndicate it is the right approach. I experimented with Medium,
Blogger (ages ago) and always found it a bit of a bitter pill to swallow that someone else has ownership
over my content. So I like having my own (insignificant) website that I can then point people to or
syndicate my content.

The [website to destroy all websites](https://henry.codes/writing/a-website-to-destroy-all-websites/) has
a very fitting end which I will shamelessly copy:

> You’re not crazy. The internet does feel genuinely so awful right now, and for about a thousand and 
> one reasons. But the path back to feeling like you have some control is to un-spin yourself from the 
> Five Apps of the Apocalypse and reclaim the Internet as a set of tools you use to build something you 
> can own & be proud of — or in most of our cases, be deeply ashamed of. Godspeed and good luck.

That was the weakly link, goodbye.