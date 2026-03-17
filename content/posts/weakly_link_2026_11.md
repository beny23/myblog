---
title: "Weakly Link 26/11"
date: 2026-03-17
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_11_title.jpg"
---

This week we're looking at zero days, zero reason for wearing Meta Glasses, zero reason to like AI slop.
Let's dive in.

# Look What You Made Us Patch

The Google Threat Intelligence [Zero-Day review](https://cloud.google.com/blog/topics/threat-intelligence/2025-zero-day-review) 
came out at the beginning of March and I thought it was interesting for a good few reasons:

- The number of zero days actively exploited looks to be fairly steady over the last few years
- Security and Networking technologies are about half of the enterprise-related targets. I guess it is easy to
  point and laugh at the security vendors, but their products make up your network edge, so by definition are much
  juicier targets than others
- The exploitation by vendor graph should make those smug hipsters "ah, but I'm on a Mac" pause to think. 
  The number of Apple zero days was bigger than Fortinet and Ivanti combined. I'm one of those hipsters - minus
  the illusion about Macs being somehow more secure. To be honest, I'm not young or fashionable either. And I
  hope I'm not that pretentious ;-)

I also thought it quite interesting on how the recommendation on how to reduce the risk of zero days
is depressingly familiar:

- Use proper network segmentation
- Monitor execution flows
- Apply Patches Quickly
- Deploy Canary Tokens
- Maintain a Software Bill of Materials (SBOM)
- Do not click on links or attachments from unknown senders

Hasn't that been the same advice for ages and ages? I guess, if world news teaches us anything we're pretty
incapable of learning from history, so it should surprise. After all SQL injection is still a common attack vector.

# Rule of 2

The google report highlighted another thing: The
[Rule of 2](https://chromium.googlesource.com/chromium/src/+/main/docs/security/rule-of-2.md) of securely
parsing untrust input (this is in context of the Chromium developer but it applies elsewhere).

The rule is simple, pick no more than 2 of:

- untrusted input
- unsafe implementation language (think memory safety)
- high privilege

It comes with a lovely image that describes it pretty neatly:

![Rule of 2 Doom](/images/weakly_link_2026_11_rule_of_2_doom.png)

# Isn't it IDORable?

Next we've got a duo of stories from the BBC about being able to access someone else's data accidentally:
We've got [Lloyds Banking Group](https://www.bbc.co.uk/news/articles/c4g23npxpwgo) and [Companies
House](https://www.bbc.co.uk/news/articles/c5y41p0dy1wo) undoubtedly getting some tough love from
the Information Commissioner's Office! (well, no, sorry, it's [called Information Commission
now](https://www.reedsmith.com/our-insights/blogs/viewpoints/102mi7r/icohno-saying-goodbye-to-the-information-commissioners-office/)).

At first glance, both of those fell foul of a cockup rather than something actively malicious. But
am I the only one that thinks the BBC calling it a "glitch" in both instances is underplaying the seriousness?

> The glitch meant they were able to view charges and payments made by other people.

and

> Companies in the UK are being urged to check their details following [a major glitch on the Companies
> House website](https://www.gov.uk/government/news/update-on-companies-house-webfiling-security-issue),
> external which potentially exposed the personal data of millions of firms.

I don't know, maybe it's just me, but a glitch sounds cute, and I don't think either of those are cute.

# Wearing Pervert Glasses? Feeling Insecure Much?

Some weeks ago I wrote about [my distaste for Zuckershades]({{< ref "weakly_link_2026_04.md" >}}), now there's
another reason to be concerned: After the Svenska Dagsbladet's [investigation on low paid Kenyan workers viewing
intimate footage](https://www.svd.se/a/K8nrV4/metas-ai-smart-glasses-and-data-privacy-concerns-workers-say-we-see-everything)
the IC~~O~~ is now [having a look](https://www.bbc.co.uk/news/articles/c0q33nvj0qpo). I bet Meta is quaking in
their boots.

It does highlight one interesting thing though. If you accidentally leave your pervert glasses on record and make 
Kenyan "data labellers" watch your dick pics on the toilet, what's stopping them from taking note of your credit card details
as you unwittingly film your wallet, or watch you type in passwords?

Eurgh.

# AI slop part 1

Next, we're looking at some AI slop. Firstly, the "need to map between different security framework" geek in
me was quite heartened when I came across the [OpenCRE](https://opencre.org/) project on the OWASP slack. And
immediately disheartened when I found the following [Pull Request on their GitHub 
repo](https://github.com/OWASP/OpenCRE/commit/d76e819d79f879c7107e1f35b027cccf57d6bcac):

> Added guidelines for avoiding trivial UI issue reports and LLM-generated contributions. Emphasized the 
> importance of quality over quantity in contributions and outlined expectations for pull requests.

I guess it is a sign of the times to see maintainers have to write the following guidance:

> **Note:** Due to a wave of low effort, poorly tested and often destructive AI-powered issues often created 
> only for the sake of opening a pull request, we will be aggressively closing both issues and pull requests 
> that link to issues not acknowledged by the maintainers.

# AI slop part 2

Unfortunately, GenAI is not just destructive by submitting low quality pull requests, it is also destructive
by submitting low-life pull requests (see what I did there). [Trivy was 
hit](https://github.com/aquasecurity/trivy/discussions/10265) by a campaign of OpenClaw bot exploiting 
vulnerable `pull_request_target` GitHub actions. The discussion thread makes for some interesting reading.

> The attack chain actually began with PR #10252, created on Feb 27 at 00:18 UTC — before the hackerbot-claw 
> PR and the VSCode extension advisory. Shortly after that PR, we observed unauthorized API activity using a 
> compromised PAT, which escalated to the release deletions, repository rename, and the malicious VSCode 
> extension publish.

# AI slop part fun

Last but not least, [a website](https://malus.sh) that I have a feeling, some people will take at face value. 
I mean, what's not to like about a service like this:

> Our proprietary AI robots independently recreate any open source project from scratch. The result? 
> Legally distinct code with corporate-friendly licensing. No attribution. No copyleft. No problems.

I love the testimonials, like this one from Patricia Bottomline from MegaSoft Industries:

> "Our lawyers estimated \$4M in compliance costs. MalusCorp's Total Liberation package was \$50K. The board 
> was thrilled. The open source maintainers were not, but who cares?"

Though to be fair, the integration to stripe wants to charge me some real money and send it to the 
"bblxddyhvwiv Institute". Hmmmmm.

That was the weakly link, goodbye.