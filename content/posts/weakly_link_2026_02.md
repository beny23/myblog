---
title: "Weakly Link 26/02"
date: 2026-01-12
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_02_title.jpg"
---

My post is a little late this week as I went away with the scouts. Everyone else was in the bunkhouse, I was in a tent.
Something about ratios. And I brought the summer sleeping bag. Freezing. Alone. Kind of like America must feel like.

![late night tent](/images/weakly_link_2026_02_late_night_tent.jpg)

What does America have to do with tents, I hear you ask? Well, now that the US has abandoned an inclusive
big tent approach and focuses more on pissing on everyone else's tents, according to Cory Doctorow
(of enshittification fame) it has handed us [a golden opportunity](https://pluralistic.net/2026/01/01/39c3/#the-new-coalition)
to break free of American dominance of technology. He gave a speech at #39c3 about Trump of all people
having given the rest of the world an opportunity to reject of one of the most insidious pieces of
regulations that is giving US companies such a stranglehold over the tech sector.

> Under anticircumvention law, it's a crime to alter the functioning of a digital product or service, unless 
> the manufacturer approves of your modification, and – crucially – this is true whether or not your modification 
> violates any other law.

> Anticircumvention law originates in the USA: Section 1201 of the Digital Millennium Copyright Act of 1998 
> establishes a felony punishable by a five year prison sentence and a $500,000 fine for a first offense for 
> bypassing an "access control" for a copyrighted work.

These laws made it possible for John Deere to make tractors that can't be repaired without paying the 
company. This is why there's BMWs that can't have automatically dipping headlights without paying a subscription.
Sure, a mechanic could probably unlock it, but it would be a crime.

It all started with the Digital Millennium Copyright Act (DMCA), which is why we can't make a copy of music
we buy, or films, and are legally not allowed to tinker with the software that we own. Sorry. We never
own software, we just licence it.

And those laws that started with the DMCA, made their way across the world. Every country has one. How? Because
giving the US dominance in digital doesn't sound like a good idea for other countries? Well, if a country didn't sign
it, the US trade representatives would threaten tariffs.

And that's it. Now that Trump throws around tariffs, why not repeal those laws? That would be an interesting
smack in the face for those same tech companies that buy million dollar seats at the Orange Inauguration.
Would be nice to loosen the grip of those FAANGs around the neck of, well, everyone.

I sure hope I'm not coming across as actually understanding the underlying legalese, but it kind of makes sense as
a reaction when European ICC judges can have their credit cards cancelled and inboxes frozen because the US says so
(see [two weeks ago]({{< ref "weakly_link_2025_52.md" >}})).

Sounds fabulous. Will Europe - especially the UK - get their act together and do what's best for them rather
than hanging on to a notion that Daddy US will look after them. Erm, I'm not hopeful.

# Rant over, onto more pleasant things

Next topic is security. Pleasant, well, maybe not.

I enjoyed reading [Brian's article](https://krebsonsecurity.com/2026/01/the-kimwolf-botnet-is-stalking-your-local-network/) 
about how a supplier leaving debug mode in the cheap device that you bought and hooked onto your local wifi, 
will be used to infect other cheap devices, so your TV becomes part of a botnet.

> Consider the following scenario, in which the mere act of allowing someone to use your Wi-Fi network 
> could lead to a Kimwolf botnet infection. In this example, a friend or family member comes to stay with 
> you for a few days, and you grant them access to your Wi-Fi without knowing that their mobile phone is 
> infected with an app that turns the device into a residential proxy node. At that point, your home’s 
> public IP address will show up for rent at the website of some residential proxy provider.

# Nightmare, eh?

Talking of nightmares, [Ni8mare](https://www.cyera.com/research-labs/ni8mare-unauthenticated-remote-code-execution-in-n8n-cve-2026-21858)
shows how n8n a popular ~~workflow automation~~ agentic system builder (get with the times, need to use the correct
words, right?) can be abused to spill secrets which makes it possible to go from no access to full admin access.

I enjoyed the detailed technical write-up on what kind of assumptions were exploited. Initial access is via
webhooks that can be abused to put arbitrary files into a knowledge base. Including the config files with the 
keys for creating the JWT token. Then the chat interface is used to extract the information. 

The mitigation depressingly echoes most security incidents: 

> 1. Update n8n to version 1.121.0 or later.
> 2. Don’t expose n8n to the internet unless absolutely necessary.
> 3. Require authentication for all Forms you create.

# Closed Too Soon

The next piece we'll look at this week has two interesting components. 
[Notion and HackerOne](https://www.promptarmor.com/resources/notion-ai-unpatched-data-exfiltration). First there
was a Notion vulnerability where data could be exfiltrated by indirect prompt injection. In this case it was
the old "white instructions on white background in the CV" trick. The exploit was to have the AI craft a
URL to an image that then gets called. But while Notion correctly asked the user to verify the URL, somehow
it would contact the URL before asking the user. That way made it possible to extract potentially
sensitive data.

The other interesting part of the story was that the vulnerability was disclosed via HackerOne, and triaged
as "Not Applicable". Straight after the vulnerability report was published (and subsequently hit the
HackerNews front page), the Notion team got in touch, verified the vulnerability and fixed it. Interesting
conundrum here, isn't there. You want to use an external supplier to validate vulnerability reports because
that's what they're good at, but what if they make a mistake. Another kind of supply chain attack vector.

# More Hallucination

Next, a piece by Casey on how to [spot a hoax](https://www.platformer.news/fake-uber-eats-whisleblower-hoax-debunked/).
The particular hoax in question was the "confession of an UberEats developer on how to exploit their drivers".

I mean with enshittification everywhere, this doesn't even sound far-fetched:

> “If a driver usually logs on at 10 PM and accepts every garbage $3 order instantly without hesitation, 
> the algo tags them as ‘High Desperation.’ Once they are tagged, the system then deliberately stops 
> showing them high-paying orders. The logic is: ‘Why pay this guy $15 for a run when we know he’s 
> desperate enough to do it for $6?’ We save the good tips for the ‘casual’ drivers to hook them in and 
> gamify their experience, while the full-timers get grinded into dust.”

(that's Casey quoting the hoaxer). It all kind of fell apart when good old-fashioned journalism kicked
in and the verification that the guy was an UberEats developer fell down on the fact that photo of the employee 
badge had the employee title printed on. This wouldn't happen as it would be expensive to print a new pass 
every time someone gets promoted!

Kind of ironic, don't you think?

# Code isn't the bottleneck

I very much enjoyed [Rob's take](https://blog.robbowley.net/2026/01/05/coding-has-never-been-the-bottleneck/) on 
why the idea that GenAI has eliminated coding as a bottleneck makes no sense. Especially as it's never been
the bottleneck

> This was already apparent even earlier. Maurice Wilkes, one of the pioneers of stored program computing, 
> later reflected in Memoirs of a Computer Pioneer his realisation in the late 1940s, that "a good part 
> of the remainder of my life was going to be spent in finding errors in my own programs." From the very 
> beginning, debugging and verification, not writing code, dominated effort.

Hear hear. Tip for young developers: Always have two copies of the "Mythical Man Month" to had, so when
someone tells you that you need to produce more lines of code quicker, you can give them both copies, just
so they can read it quicker.

# To Finish

I like [this webpage](https://worstofbreed.net/patterns/). All too often people want to talk about
"what does good look like?" That's nice enough, but to really get the discussion going, it's
useful to know what really bad looks like.

A lot of the cards made me giggle, but this one is my favourite:

> "We encoded passwords with Base64. That is secure, right? At least it looks cryptic."
> 
> The auditor asks: 'Is the data encrypted?' You say: 'Yes, with a proprietary algorithm.' He checks it off.

That was the weakly link, goodbye.