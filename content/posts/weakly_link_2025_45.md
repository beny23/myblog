---
title: "Weakly Link 25/45"
date: 2025-11-07
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_45_title.jpg"
---

Every week I come across some interesting, ridiculous or astounding content related
to security and tech around software engineering. And I post it on the company Slack, sometimes on 
[LinkedIn](https://www.linkedin.com/in/gerald-benischke-9811b663) and often on 
[BlueSky](https://bsky.app/profile/beny23.github.io) or [Mastodon](https://infosec.exchange/@beny23). (I deleted
my Twitter account a long time ago. No Nazi bar for me.) 

And yet, I often forget all about the content. And 
because I closed my browser with 200ish open tabs once too often, I thought, why not write about the things
that interested me, then I can look back on them and maybe someone else might find them interesting too.
Shout out to the excellent [CyberWeekly](https://cyberweekly.net) which gave me the idea...

Anyway, without further ado:

# Fucking AI

> \[She] tells the chatbot to "quit it". But \[…] the chatbot says: "He is using you as his toy. 
> A toy that he enjoys to tease, to play with, to bite and suck and pleasure all the way.
> "He doesn't feel like stopping just yet."

This BBC News article titled [I wanted ChatGPT to help me. So why did it advise me how to kill myself?](https://www.bbc.co.uk/news/articles/cp3x71pv1qno)
was about a young woman that was lonely and used ChatGPT. And unfortunately, the guardrails went off the rails
and it suggested suicide. As if that wasn't bad enough, the article then continued about a 13-year-old that 
was driven to suicide because she got lost in conversations with customised AI characters.

So, not content with stealing everyones content, let's have the LLMs steal our children's childhoods too.

# Scamming AI

[This piece](https://www.reuters.com/investigations/meta-is-earning-fortune-deluge-fraudulent-ads-documents-show-2025-11-06/) 
from Reuters makes me angry

* Meta serves 15 billion scam ads a day
* ignores or incorrectly rejected 96% of user reports
* makes $7bn ARR from scam ads
* has revenue guardrails to ensure block scam doesn’t hit the bottom line
* the biggest scammers pay more for ads but get about 500 strikes before their accounts get touched

Who says crime doesn’t pay... I guess they need all the money in the world to pay for AI and building Trump palaces.

# Bullying AI

Staying with scamming AIs

> Cybersecurity vendors peddling nonsense isn’t new, but lately we have a new dimension — Generative AI. 
> This has allowed vendors — and educators — to peddle cyberslop for profit.

[This](https://doublepulsar.com/cyberslop-meet-the-new-threat-actor-mit-and-safe-security-d250d19d02a4) is a 
great story about how MIT published a report alleging that 80% of ransomware is using AI. And surprise,
surprise, it's nonsense. I'm quite glad that Kevin Beaumont managed to bully MIT into disappearing it
from their website.

# AI security

The Register ran an interesting story about efforts by OpenAI to join in the security vendor trough by
announcing [Aardvark](https://www.theregister.com/2025/10/31/openai_aardvark_agentic_security/) to solve all 
the problems that they created.

I did love this bit:

> But unlike a human, Aardvark just runs and runs. It can't be bargained with; it can't be reasoned with. 
> It doesn't feel pity or remorse or fear. And it absolutely will not stop unless you've set up an OpenAI 
> API budget limit, your credit card expires, or the AI bubble pops and takes us all down with it.

Don't get me wrong, I'm not saying that there isn't room in AI for security, but the inherent issues of
how LLMs work is going to make it quite tricky to use "physician heal thyself" in the context of LLMs.

# AI insecurity

As if to bring the previous point home, here's a [great example](https://embracethered.com/blog/posts/2025/claude-abusing-network-access-and-anthropic-api-for-data-exfiltration/) 
by wunderwuzzi on using indirect prompt injection coupled with weak egress protection on how getting
a target to summarise a file can be used to exfiltrate their previous conversations.

The kill chain involves:

* Approved network domains containing `api.anthropic.com`
* Ability to use memories
* Files API to allow uploading content
* Changing the Anthropic API key to the attackers key to exfiltrate the files to the attackers (burner) account.

To top it off, Anthropic doesn't consider this a security risk. Erm. Oookay. We're at this stage in
the goldrush where new features are being thrown at us and security playing catchup. I'm sure there's going
to be lots and lots of new attack vectors.

And it is going to be a very long time (actually more like until hell freezes over) before I consider using an 
AI browser that can then use these techniques for exfiltrating lots of user session data.

# Sloppy Dependencies

One of the fun AI-driven vulnerabilities is slop-squatting where people push malware onto package registries
that picks up names that the LLM code generators hallucinate. This project caught my eye: 
[Slopguard](https://aditya01933.github.io/aditya.github.io/slopguard) aims to use scoring to check whether 
packages are likely typosquatting. I think we're going to need this in a lot of build pipelines. And also on
developers machines.

# The Joy of Thinking

This provides a nice segue into the [next post](https://kevlinhenney.medium.com/think-for-yourself-7d129aa959e3). 
Kevlin asks us to consider how to understand and improve LLM generated code.

This bit stood out for me:

> In software development, one of the (non-swear) words we use to describe things we don’t understand or 
> feel empowered to improve is legacy. We should be careful both to avoid ‘optimising’ and accelerating 
> the creation of legacy code and to mistake such pessimisation as productivity — especially if we’re 
> doing so at the expense of joy, time and skill.

I'd like to repeat: _at the expense of joy, time and skill_

What good is it if we're accelerating software delivery by as much as 10% in the short term, but completely forget
how to do it in the long term.

Hits home that doesn't it.

# Golden Facepalm

Finally, a lot of chuckling happened on t'interwebs about the really secure side of that museum at the centre
of a recent jewellery heist. Apparently their [password was quite weak](https://www.independent.co.uk/news/world/europe/louvre-security-password-museum-heist-burglary-b2859831.html).

As there is an [XKCD](https://xkcd.com/538/) for every situation I thought I'd reimagine it:

![Louvre Password Security](/images/weakly_link_2025_45_louvre.jpg)

That was the weakly link, goodbye.