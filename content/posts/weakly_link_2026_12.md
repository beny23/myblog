---
title: "Weakly Link 26/12"
date: 2026-03-23
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_12_title.jpg"
---

This week it gets all too depressing. We start by the fact that AI didn't in fact change everything but just
made it more important to do the right thing. How politicians may not have the right kind of understanding
to make decisions that actually make sense. How one (orange) politician doesn't understand how the world
actually works and how we can all laugh and point at McKinsey for when they predicted how essential it
was to go all in on the metaverse.

# Iterating One Shot

Stripe knows a thing or two about software development. It's been disrupting the online payments. I found
this set of blog posts about [Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents)
and [One-Shots](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) quite interesting.

But let's step back a little. What is a One-Shot? It's all about giving the coding agent one bit of instruction,
and it then goes away and completes the task. No, this isn't a "this changes everything" kind of take. The
interesting bits in the series came in with the fact that the agents are using the pre-existing investment
in developer productivity:

> While we build minions with the goal of one-shotting their tasks, it’s key to give agents automated feedback 
> that they can iterate against to make progress. Stripe’s enormous preexisting battery of tests—over three 
> million of them—can provide this feedback. However, while a pushed branch will run all relevant tests in CI, 
> we don’t want to rely too heavily on CI for all our code feedback.

There are other things to like - the agents make use of "devboxes":

> A Stripe devbox is an AWS EC2 instance that contains our source code and runs services under development. 
> Most human-written Stripe code is already produced within an IDE that’s remotely connected to a devbox via SSH. 
> In DevOps terminology, devboxes are “cattle, not pets”: they’re standardized and easy to replace, rather than 
> bespoke and long-lived.

And those devboxes can be spun up within something like 20 seconds. The other thing is that the devboxes are 
quarantined, which means they can't be used to access production data. Because you wouldn't want a random
developer to just poke around sensitive financial data.

So all of those things are pretty standard, but just now dressed up in "one-shotting" and "AI-driven development".
Seems to me the lesson there is that if you invest into decent tooling for your meatbags, the robots will
also benefit.

# Moving Fast and Not Breaking Things

Talking of investing in doing the right thing upfront, I enjoyed reading [Leah's article about moving 
fast](https://itrevolution.com/articles/why-move-fast-and-break-things-is-wrong-lessons-from-aws-ebay-and-google-on-speed-with-safety/).

> “Move fast and break things” became Silicon Valley’s most famous mantra. It represented the boldness, 
> the disruption, the willingness to sacrifice everything at the altar of speed. But here’s the uncomfortable 
> truth that tech giants learned the hard way: breaking things doesn’t make you faster. It makes you slower, 
> more expensive, and far less safe.

Next time someone suggests that you should just enable developers to do whatever they want to get things done
and fix it later, the following should be tattooed on the inside of their eyelids:

> The question isn’t whether to prioritize speed or safety. The question is understanding how they enable 
> each other—and recognizing when optimizing for one dimension actually undermines both.

As per a lot of the IT Revolution content, there's quite a lot of case studies. And I rather liked the idea
that investing heavily in OTA (Over the air) software upgrades in the car industry enabled massive cost 
reduction. 

> Those who decided OTA was strategic but didn’t sufficiently invest saw “bricked” cars needing dealer towing 
> when upgrades failed and vehicles wouldn’t turn on. But manufacturers who properly invested in OTA realized 
> that significant up-front cost resulted in reduced long-term costs.

The quote that really made me sit up was

> Once software defects became the number one cause of car recalls, manufacturers with OTA capabilities 
> gained major cost structure advantage.

I guess as a software developer, there is a reason I prefer old cars with as little electronics as feasible!

# Talking of bugs

[Last week]({{< ref "weakly_link_2026_11.md" >}}) we talked about how Trivy got hit by an attack on their 
build infrastructure was used to push out malicious updates. This week, there was [another 
issue](https://github.com/aquasecurity/trivy/security/advisories/GHSA-69fq-xp46-6x23):

> This incident is a continuation of the supply chain attack that began in late February 2026. Following the 
> initial disclosure on March 1, credential rotation was performed but was not atomic (not all credentials 
> were revoked simultaneously). The attacker could have used a valid token to exfiltrate newly rotated secrets 
> during the rotation window (which lasted a few days). This could have allowed the attacker to retain access 
> and execute the March 19 attack.

> The attacker force-pushed 75 existing tags in `aquasecurity/trivy-action` to new malicious commits. Any GitHub 
> Actions workflow referencing a tag like `aquasecurity/trivy-action@0.33.0` automatically resolved to whatever 
> commit that tag pointed to at runtime. Once the tag was moved, the workflow consumed attacker code without 
> any change in the workflow file itself.

Ouch! I hope you have runbooks that look at how to rotate secrets such that a compromised one can't actually
be used to maintain access.

# Talking of compromising positions

Next we've got a pair of uncomfortable looking security vulnerabilities. Both give similar vibes:

- [Unauthenticated Remote Code Execution in Langflow via Public Flow Build 
  Endpoint](https://github.com/advisories/GHSA-vwmf-pq79-vjvx)
- [Critical Authentication Bypass in pac4j-jwt - Using Only a Public 
  Key](https://www.codeant.ai/security-research/pac4j-jwt-authentication-bypass-public-key)

Both look like remote code execution possible without needing to authenticate. Those are the worst kind of
vulnerabilities. So get patching AND checking your audit logs to see whether someone might have exploited
you already.

# The age-old question

I rather liked Rupert's [article](https://www.theregister.com/2026/03/16/opinon_column_age_verification/) on
the Register about age verification. I guess there's been a bit of a moral panic going round about whether
we "think of the children" enough. And as per usual, politicians don't know enough about technology to come
up with a decent solution. Or any solution that can be considered workable.

Much like the question of having a backdoor for encryption without breaking encryption, I guess age verification
is the latest way of politicians to show off cluelessness. Much like in Britain - where age verification has meant
people using VPNs whether they're reputable or not, or submitting their passports to dodgy
age verification providers - California is now demanding that:

- any general purpose computing device needs to do age verification
- app stores must use that verified age
- the connection between said device and app store happens via a hand-wavy "digital signal"

Of course, there are sanctions and fines. 

Now, let's step back a bit here. The Register probably is overegging the pudding a little. I can't see that retro
computing will suddenly stop. But the question would be how much additional complexity there is in offering
an app or a SaaS product if you have to ensure that you consume the "digital signal" from all the different
Operating Systems and mobile platform. Somehow I can't see Linux, MacOS, Android, Windows and iPhones
all having the same standard.

Still, I don't think there's much argument that when it comes to listening to expert advice, politicians more
often than not are advised by the very consultancies that then sell expensive solutions to the same 
government. Oh well.

# Talking about incompetence

I try to keep this blog focussed on tech, not politics, but sometimes the urge to scream out loud just
gets overwhelming. I don't know what kind of lunatics are running the asylum, but [this 
piece](https://no01.substack.com/p/march-19-21-god-is-a-comedian) made me laugh:

> Friday’s press gaggle. Barely exaggerated: at 12:03 PM, President Trump told reporters he wanted a ceasefire 
> with Iran. At 12:05 he declared victory. At 12:07 he announced he was sending Marines. At 12:08 he said 
> no boots on the ground. At 12:11 he said he did not want a ceasefire. At 12:16 he declared victory again. 
> At 12:17 he asked for a ceasefire. At 12:23 he told NATO they were cowards. At 12:29 he said Iran was begging 
> for a ceasefire. At 12:31 he said everything was perfect. At 12:36 he said $500 oil was a good thing. At 
> 12:37 he demanded Iran open Hormuz. At 12:39 he said Hormuz was never closed. At 12:41 he said the US was 
> not at war with Iran. At 12:42 he declared victory in Iran.

# Some positive news

After all the doom and gloom, it's time to finish with some light relief. Just consider 
[McKinsey waxing lyrical about the Metaverse](https://www.mckinsey.com/capabilities/growth-marketing-and-sales/our-insights/value-creation-in-the-metaverse)

> With its potential to generate up to $5 trillion in value by 2030, the metaverse is too big for companies 
> to ignore.

Though I think collective we've decided that Zuckerworlds haven't got legs and nobody decided to generate any of
those trillions. So then Meta was going to shut it down. But wait, then they [reversed the 
decision](https://www.independent.co.uk/tech/meta-metaverse-horizon-worlds-b2942054.html)

> But just days after, Andrew Bosworth, Meta’s chief technology officer, said that it would actually remain 
> available. “We have decided, just today in fact, that we will keep Horizon Worlds working in VR,” he said in 
> a question and answer session on Instagram, in response to a user who said they were “heartbroken” by the 
> decision to shut it down.

A user? A single one? Was their name Zark Muckerberg by any chance?

That was the weakly link, goodbye.