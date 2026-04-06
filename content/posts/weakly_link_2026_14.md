---
title: "Weakly Link 26/14"
date: 2026-04-05
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_14_title.png"
---

This week we have a look at the current chaos. Be it political or technical, we're going through some radical 
changes. And I can't help but think, if this is what progress looks like, oh crap. 

# Supply Chain Chaos

We start by having a look at an article by Ian about the [Mad Emperor](https://iandunt.substack.com/p/the-mad-emperor-has-triggered-chaos).
No prizes for guess who is meant there. From the outside in, it really looks like there's no plan or no idea
about the kind of problems the attack on Iran is causing. Yes, the oil price is going up, but that's not the worst
of it:

> The Strait of Hormuz is not just an oil chokepoint. It is much more than that. In addition to oil it is a 
> key transit point for liquified natural gas, aluminium, helium, petrochemicals and fertiliser. What we are 
> seeing is akin to no-deal Brexit, in military form and on a global scale. It is a system-wide event with 
> primary, secondary and tertiary causal waves which touch nearly all areas of the economy.

These shockwaves take time to go through. We're seeing restriction on petrol in some countries. But what will
happen when fertiliser will run out. 

> Fertiliser firms in India, Bangladesh and Pakistan have shut down production without access to natural 
> gas from Qatar.

That natural gas can't be shipped through pipelines.

> There will then be a third inflationary impact from restrictions on ongoing exports. Brazil, for instance, 
> imports 85% of its fertiliser. It is then uses it in soybean production, where it is a major exporter and 
> global price-setter. Three quarters of China’s soybean imports are from Brazil, which it uses to feed 
> livestock. The inflationary wave will travel overseas, hitting country after country, and bouncing off to 
> hit another country, in a series of interlaced economic effects.

Much like Brexit, we're not seeing the real effects immediately, it'll take some time to filter through the system.
And it will hit the poorest hardest.

> It will be felt in Sri Lanka during the Maha rice harvest, in Bangladesh during the Boro rice season, in 
> India ahead of the Kharif season and in Egypt, Sudan and Sub-Saharan Africa.

And the worst bit is, it wouldn't even matter if the war ended tomorrow. The damage is done.

> Even if the war ends tomorrow, the pain is coming. The damage has already been inflicted: on extraction, 
> capacity, distribution. It will not be restored in months, it will take years. And in that time we will all 
> suffer, to some extent or other. All because the emperor woke up one morning and decided to start a war.

# Disaster Recovery

Of course, the war has more direct supply chain implication. If your supplier is AWS in the Middle East, well,
you might have had some problems as [this thread on reddit shows](https://www.reddit.com/r/aws/s/9Gjg1FahcT).

> > ME-SOUTH-1 has been severely impacted by the situation in the Middle East.
> 
> "Severely impacted" == Blown up with a drone
> 
> AWS officially calls it a "localized power issue"

They had an application running in ME-SOUTH-1 only. Now before you go fingerwagging "you should have just done
multi-region" please bear in mind that some of the data sovereignity restrictions in the region don't make that
the easiest thing.

Serves as a reminder that having a tested Disaster Recovery plan is worth having. And AWS won't be able to
bail you out, because they'll just point to their recommendations "you should have bought multi-region"...

# Cloud Disaster

Speaking of disasters, here's a couple of recent articles that don't necessarily show Azure in a good light:

First, [this piece](https://www.propublica.org/article/microsoft-cloud-fedramp-cybersecurity-government) by 
ProPublica on how Microsoft didn't necessarily leave people impressed:

> The tech giant’s “lack of proper detailed security documentation” left reviewers with a “lack of confidence in 
> assessing the system’s overall security posture,” according to an internal government report reviewed by 
> ProPublica.
> 
> Or, as one member of the team put it: “The package is a pile of shit.”

The article goes on to explain how the Government Community Cloud (GCC) High faced a rather frustrating process
of getting properly reviewed.

> “After three years of collaboration with the Microsoft team, we still lack visibility into the security gaps 
> because there are unknowns that Microsoft has failed to address,” Conrad wrote in an October 2023 email.

And in the end, it was approved:

> Despite the findings, to the FedRAMP team, turning Microsoft down didn’t seem like an option. “Not issuing an 
> authorization would impact multiple agencies that are already using GCC-H,” the summary document said. The 
> team determined that it was a “better value” to issue an authorization with conditions for continued 
> government oversight.

The [second piece](https://isolveproblems.substack.com/p/how-microsoft-vaporized-a-trillion) - actually a 5-part
series of posts - appears to be by a senior Microsoft Azure engineer having a rant how his recommendations were ignored, 
gaps in security hushed up and problems solved by getting more people to fix more things manually.

> I later researched this further and found that no one at Microsoft, not a single soul, could articulate why 
> up to 173 agents were needed to manage an Azure node, what they all did, how they interacted with one another, 
> what their feature set was, or even why they existed in the first place.

Umm, not a great start. It goes on to say that Azure was initially positioned to be a stack where everything is
automated and humans should not have to, or not even be able to, intervene. But delays, and overpromises to the
market resulted in rushed teams, technical debt that was pushed further and further down the backlog.

> From my years with one of the original contributors to the Fabric, I learned that touching the nodes by hand 
> was also strictly off-limits: the original design intent was that Azure would operate without human 
> intervention.

That led to shortcuts and more shortcuts

> Cutler’s vision of a "no human touch" cloud service unfortunately never materialized, as the article mentions 
> "hundreds of interactions" each month for the government clouds alone.

Which ultimately leads to

> The answer is now simple: the software didn't work as well as hoped, in large part because the system was rushed 
> under intense pressure.
> 
> Cue the post-launch talent exodus, its replacement by people of very different experience levels, and you end 
> up with a system that over-promises and under-delivers, drowning in unsolvable problems.

It's not just reliability that suffers when shortcuts are taken:

> Running a web server on the host OS with unsecured endpoints exposed to guest VMs, whether signed or not, 
> poses a greater security risk.

> My recommendation was to remove WireServer and IMDS from the nodes entirely, a view shared without reservation 
> by a VP security architect, author of a popular book about threat modeling, with whom I shared my concerns.
>
> Upon further digging, I discovered that WireServer was maintaining in-memory caches containing unencrypted 
> tenant data, all mixed in the same memory areas, in violation of all hostile multi-tenancy security guidelines.

Ouch.

Now, I would like to take the above two articles with a pinch of salt. The latter piece was written by someone who
was sacked by Microsoft, so is bound to be a bit salty \[geddit]. And the former sounds like any government 
procurement exercise. Still, as engineers we tend to have this vision that hyperscale cloud providers know what 
they are doing, always follow best practice and do the right thing. At the end of the day, they are mere mortals
with tight budgets and unrealistic deadlines. And, at the scale of the software needed to manage hyperscalers, that 
should come as no surprise that a lot of it is held together by gaffer tape and crossed fingers. But then again, 
have you ever worked in a large corporation where that wasn't the case?

# Traditional Supply Chain Trouble

These days a supply chain attack is hardly news anymore, but I thought the following two were interesting:

First, we've got the continuing fallout from the Trivy attack, where [LiteLLM was 
poisoned](https://semgrep.dev/blog/2026/the-teampcp-credential-infostealer-chain-attack-reaches-pythons-litellm/). 
I found this interesting because it shows that one target can give access to the next, which gives access to the 
next. In this case, the Trivy attack led to permissions to infect LiteLLM with an infostealer. LiteLLM is interesting
because it is used to abstract different LLM providers, and getting into that supply chain can give away more
credentials. And on it goes.

Another [supply chain attack impacted Axios](https://github.com/axios/axios/issues/10636), which is a popular http 
library. Luckily it was caught after only a couple of hours. What I found interesting was the fact that it 
seemed to have been [a tailored social engineering attack](https://simonwillison.net/2026/Apr/3/supply-chain-social-engineering/)
against a maintainer.

> - they reached out masquerading as the founder of a company they had cloned the company's founders likeness as 
>   well as the company itself.
> - they then invited me to a real slack workspace. this workspace was branded to the companies ci and named in a plausible manner.
> - they scheduled a meeting with me to connect. the meeting was on MS Teams.
> - the meeting said something on my system was out of date. i installed the missing item as i presumed it was something 
>   to do with teams, and this was the RAT (Remote Access Trojan).

Simon's piece observes:

> That’s a very effective scam. I join a lot of meetings where I find myself needing to install Webex or 
> Microsoft Teams or similar at the last moment and the time constraint means I always click “yes” to things 
> as quickly as possible to make sure I don’t join late.

And before the "should have 2FA enabled" brigade arrives: the maintainer did have 2FA enabled for npm. But
the RAT stole the credentials anyway. 

# LinkedIn's Attack

Sometimes it is quite difficult to see where telemetry ends and an attack begins. Real User Monitoring is often
used to find problems with your software. And it basically allows companies to replay what exactly you're doing,
how long you linger over a button or whether you frustratedly move the mouse about. So far so Orwellian. But
is it ok for LinkedIn to ship extensions that try to determine what Chrome Extensions you've got installed on
your browser? [This](https://browsergate.eu/how-it-works/) is how they do it.

No, I can't think of a good reason either. And no, I don't believe the "It's Fraud Prevention" angle either.

# Looking to the future

I came across [this piece by Thoughtworks](https://www.thoughtworks.com/content/dam/thoughtworks/documents/report/tw_future%20_of_software_development_retreat_%20key_takeaways.pdf)
about the future of software. Now of course, it's all about AI, but I found it rather refreshing:

I like that it points out:

- Good engineering practices are essential to be successful with AI agents
- Junior developers are more rather than less valuable
- AI needs lots of context, so things like postmortems become really valuable (you all do them, right? Right?)
- Strictly typed language offer guardrails to help prevent LLM hallucinations
- Security is treated an afterthought

On security:

> The most vivid example: granting an agent email access enables password resets and account takeovers. Full 
> machine access for development tools means full machine access for anything the agent decides to do. 
> The retreat's recommendation was direct. Platform engineering should drive secure defaults by making safe 
> behavior easy and unsafe behavior hard. Organizations should not rely on individual developers making 
> security-conscious choices when configuring agent access.

I think that last bit is particularly important. Do not rely on individual developers making the right choices.
That's not to say that developers intentionally do the wrong thing when it comes to security, but as with seen
with the glut of supply chain attack above, it is all to easy to have your credentials stolen. So let's not
make that *more* likely by giving LLMs access to anything and everything and trusting them to behave. Organisations
and platform teams need to put the right guardrails in place. That means platforms need to be opinionated in
what is right and wrong. What's the quote? "Strong opinions, weakly held." 

# Strong Opinions

There were certainly strong opinions when I went to Holly's webinar about security planning. In that hour-long
rant, it was great to hear that pentesters value good documentation. And want to see plans for what should happen
when the worst happens. As a bonus she pointed us to their [How to write good 
policy](https://akimbocore.com/knowledge-base-akimbo-good-policy-guide/) guide which is full of obvious things
to do. And I mean that as a heartfelt compliment, because sometimes common sense gets forgotten about when
policies get written.

That was the weakly link, goodbye.