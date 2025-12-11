---
title: "Weakly Link 25/50"
date: 2025-12-11
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_50_title.jpg"
---

Let's start this week with many happy returns for Let's Encrypt! It's been [10 years](https://letsencrypt.org/2025/12/09/10-years)
since free TLS certificates made it much more common that things go via HTTPS rather than HTTP. When
anyone and everyone was able get a cert, it certainly reduced the barriers to adoption. These days it
is _almost_ unimaginable to have some web traffic that doesn't use TLS. Now, if only we could convince
some "security experts" to stop going on about how connecting to a public wifi will allow h4x0rs to steal all
your data.

The more likely way of losing your data is almost certainly that someone politely asks for it. Whether
it be handing them over willingly by falling for a phish, or whether some social engineers manage to convince
companies to hand over their data. And that latter possibility is not as far-fetched as you might think, as this 
[piece in Wired](https://www.wired.com/story/doxers-posing-as-cops-are-tricking-big-tech-firms-into-sharing-peoples-private-data/)
illustrates:

> “This took all of 20 minutes,” Exempt, a member of the group that carried out the ploy, told WIRED. 
> He claims that his group has been successful in extracting similar information from virtually 
> every major US tech company, including Apple and Amazon. \[...]
> 
> Exempt, adding that he can then use that information to make emergency data requests. 
> “And with a subpoena and search warrant, I can access DMs, texts, call logs. That’s someone’s full 
> life in my hands in the space of hours, depending on the response times of the company or provider.”

Maybe handing over all of our data to companies that will then share it around is something to be worried
about. And no amount of declining cookies will save you there. Sweet dreams!

# Some fun vulnerabilities

This [CVSS 10 vulnerability in Apache Tika](https://lists.apache.org/thread/s5x3k93nhbkqzztp1olxotoyjpdlps9k)
is interesting. not so much for the vulnerability itself but that it's a recurrence of a previous vulnerability
where there was a mix-up on the actual dependency that contained the problem. 
[Curating Vulnerabilities]({{< ref "curating_vulnerabilities.md" >}}) has always been tricky, and I 
will continue to maintain that having to read lots of CVEs and knowing context of how libraries are used
is key, but it's not made easier by having to worry that things are miscategorised. Mind you, I'm not trying
to throw shade on the people that analyse CVE submissions. In the ever-changing landscape [sic] of AI slop
I don't envy the analysts!

# More fun

This piece by Lyra about [clickjacking](https://lyra.horse/blog/2025/12/svg-clickjacking/) was very interesting.
She shows how clickjacking can be done purely in SVG filters. In fact, SVG filters can be used to implement
logic. I loved the journey that Lyra painted about wanting to make Liquid Glass like effects work and ending 
up with a cool attack vector.

# Talking of Liquid Glass

I liked [this rant](https://blog.jim-nielsen.com/2025/icons-in-menus/) by about Apple seemingly ignoring their
own style guidelines:

> There are a few standard symbols you can use to indicate additional information in menus...
> Don’t use other, arbitrary symbols in menus, because they add visual clutter and may confuse people.

I wholeheartedly agree, but I suppose ~~designers~~ marketeers can't help wanting a fresh new look
every few years that'll use up all those extra CPU cycles that Moore's Law gives us every other year.

# Some Serious Talk

Next we head over to AI. First of all great post by the NCSC about 
[prompt injection](https://www.ncsc.gov.uk/blog-post/prompt-injection-is-not-sql-injection) and how it
is not like SQL injection:

> A better approach to mitigating prompt injection might be to not treat it as a form of 
> code injection, but instead view it as an exploitation of an ‘inherently confusable deputy’. [...]

> Crucially, a classical confused deputy vulnerability can be mitigated, whilst I’d argue LLMs are 
> ‘inherently confusable’ as the risk can’t be mitigated. Rather than hoping we can apply a 
> mitigation that fixes prompt injection, we instead need to approach it by seeking to reduce 
> the risk and the impact. If the system’s security cannot tolerate the remaining risk, 
> it may not be a good use case for LLMs.

I think it is great how the NCSC tries to put out useful information, I always like reading their
blog posts!

# Trigger Warning

The next piece is rather serious. It's all about how just because there's AI on the box, doesn't mean
there's AI in the box. [This essay by Michael](https://data-workers.org/wp-content/uploads/2025/12/The-Emotional-Labor-Behind-AI-Intimacy-1.pdf)
explains how he made ends meet by working for a platform where he impersonated characters to fulfil
user's fantasies:

> But the confusion ran deeper than that. I began to wonder: what if I wasn't just training
> an AI companion, what if I was actually impersonating one? Maybe users thought they
> had already purchased an AI girlfriend or boyfriend, and I was the human pretending to
> be the machine pretending to be human. That would explain why the personas were so
> stripped of real detail, why everything had to remain surface-level and fantasy-driven.

Of course, there was a boatload of NDAs to prevent speaking about this. But it sounds
like exploitation of the worst kind.

> The platform would periodically test us to ensure we weren't bots; they'd ask us to prove we 
> were real humans behind the screen. But I had been living inside these fabricated personas 
> for so long that when they asked me to prove my humanity, I panicked and started defending 
> whichever fake identity I was currently operating instead.

Shame that recently the most coherent themes of techbro-driven economics is chasing those
dystopias that SF novels warned us about.

> This is about recognizing that you cannot build ethical AI on the foundation of exploited, 
> traumatized human labor. If the training data comes from people who were psychologically 
> destroyed in the process of creating it, then the technology itself is tainted from the start.

Not for the faint-hearted.

# Failure

To end with, I'll leave you with this [great piece](https://dylanbeattie.net/2025/12/08/so-you-want-to-speak-at-software-conferences.html)
by Dylan on the ins and outs of getting accepted at talks. Kind of serendipitous considering it came
in the week when I got back rejections for three of my talks submissions in two days. Oh well,
you win some, you lose some.

That was the weakly link, goodbye.