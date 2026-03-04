---
title: "Weakly Link 26/09"
date: 2026-03-04
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_09_title.png"
---

This week we're looking at how some of the traditional thinking on security (detect it, patch it, monitor it)
is no longer quite cutting it. It is interesting how cyber threats have very much moved on from malware.
In my opinion, the latest CrowdStrike threat report can be used to argue that security is not something
that can be fixed by buying a shiny security tool. It's not a technology problem, it's a social (engineering)
problem...

# CrowdStrike? More like Cloud Strike!

It's that time of the year again, when CrowdStrike releases their 
[Global Threat Report](https://www.crowdstrike.com/en-us/blog/crowdstrike-2026-global-threat-report-findings/).
There were a few interesting nuggets in there for me. Was it the fact that the threat is increasing and attacks are
on the rise? No, that's a given. It's a vendor report after all ;-). The "27 seconds to breakout" headline is, well,
headline grabbing, but it's the underlying cause that's interesting. A lot more attacks happen without Malware.
A lot more attacks happen using LOL. No, that's not Laughing Out Loud, but Living Off the Land. It is where attackers
don't bring their own tooling, but just use what is available to them. And that is increasingly your own AI
infrastructure. Why use your own model when you can use your victims model...

The other interesting tidbits were that there was a 42% increase in zero-day vulnerabilities being used before
they were published. I guess those would be minus days, I mean if you get hit by one of those, your mood will
certainly turn negative... I'll get my coat.

So what does this mean for security?

- Do you solely rely on vulnerability scanners to find attack points. Well, that might be too late already 
  considering we're now dealing with minus days.
- Do you rely on human in the loop monitoring and alerting to stop attacks? Well, that might be too late because
  the attacker has already exfiltrated your data. The report mentions Chatty Spider (oh how I do hate those
  monikers) exfiltrated data within 4 minutes of gaining access.
- Do you rely on malware detection to alert you of attacks? Well, LOL. And the report mentions that attackers
  are increasingly targeting unmanaged devices or try to find gaps in the detection.
- Do you have a SOC that detects, alerts, investigates and responds? Well, by the time you investigate, it's might
  be too late already.

So are we doomed? Well, let's put a positive spin on it. The technical controls are now so good that malware as
an attack vector has been largely replaced with social engineering. Time and time again, the big attacks don't
happen because someone picked the lock on your front door, but because you left 100 of your 50,000 keys for all to
see and you adorned it with a sticky note on it that contains your alarm code. Or they walked round the back and 
through the open back door.

I think that modelling threats, predicting where the weak points are and using defence in depth is going to
be important. Have you got lots of different layers that mitigate what can come in and out. Are you looking
at the supply chain? Do you know all the legacy IT on your estate?

Oh and one more bit. Drucker said culture eats strategy for breakfast (well, 
[he actually didn't](https://andiroberts.com/leadership/culture-eats-strategy-for-breakfast-myth) but let's not 
ruin a good quote). I think it is the same for cyber security. A good culture will beat technology. If security
is a first-class citizen in your development process, your infrastructure design and your product plans, then
it will become much harder for adversaries to slip through the net. If you view people as valuable assets
in the fight against intrusions (and give them the tools to do it), I think it will go much much better than if
there's a massive amount of mistrust between the security department and everyone else.

Having said that, I'm sure someone will leave an Excel spreadsheet with production passwords lying around
somewhere, for the next big incident...

# Helldesk software

Funny I should say this, because our next link is about watchtowr finding some pretty [bad vulnerabilities in
the SolarWinds Web Help Desk](https://labs.watchtowr.com/buy-a-help-desk-bundle-a-remote-access-solution-solarwinds-web-help-desk-pre-auth-rce-chain-s/).

Yes, that SolarWinds. Why is it always security vendors that miss the basics? Having an attack vector that
exploits deserialisation in internet exposed software shouldn't happen anymore these days. But it does,
and it will again. Same reason why SQL injection is still on the OWASP top ten even after 30 years.

It also must be frustrating for customers as the reasons why these things are vulnerable aren't new:

> Gather round, strap in, and prepare for another depressing journey of “all we wanted to do was reproduce an 
> N-day, and here we are with 0-days”.

> Deserialization issues have plagued SolarWinds’ Web Help Desk - and have supposedly been “fixed” multiple 
> times. But our opinion is that the public details surrounding these vulnerabilities have left much more to 
> be desired.

Seems like as software industry we are operating like this:

- Someone finds an issue
- We do the minimum to patch it but leave the design unchanged
- Someone else finds a related issue
- Nobody could have predicted this...

And for anyone saying that AI will fix this, I think AI will just exacerbate the issue, after all we're collectively
producing a lot more code, following a lot more of the existing patterns. If not done right, GenAI code
is instant tech debt.

# Google API keys are safe. Until they weren't.

Talking of AI fixing this, here's a story by trufflesecurity on [how google shot themselves and developers 
in the foot](https://trufflesecurity.com/blog/google-api-keys-werent-secrets-but-then-gemini-changed-the-rules).

> Google spent over a decade telling developers that Google API keys (like those used in Maps, Firebase, etc.) 
> are not secrets. But that's no longer true: Gemini accepts the same keys to access your private data. We scanned 
> millions of websites and found nearly 3,000 Google API keys, originally deployed for public services like Google 
> Maps, that now also authenticate to Gemini even though they were never intended for it. With a valid key, an 
> attacker can access uploaded files, cached data, and charge LLM-usage to your account. Even Google themselves 
> had old public API keys, which they thought were non-sensitive, that we could use to access Google’s internal 
> Gemini.

I guess that is a side effect of stuffing GenAI solutions into every product available. The fact that this
silently happened to existing keys is more than a bit shit.

# GenAI is also good

I'm not a fan of GenAI. I find it using it quite soulless and mind-numbing. But I cannot deny that it is good
at repetitive tasks that can't be done by scripts. Give GenAI models a large data set and it will find interesting
stuff. I think this is perfectly illustrated by [this post](https://www.mendral.com/blog/llms-are-good-at-sql) 
by Andrea.

> Last week, our agent traced a flaky test to a dependency bump three weeks prior. It did this by writing its 
> own SQL queries, scanning hundreds of millions of log lines across a dozen queries, and following a trail 
> from job metadata to raw log output. The whole investigation took seconds.

If we think about this, it makes a lot of sense. There are tons of examples in the GenAI model's training
data on how to write good SQL queries. So it makes sense to use a generic language rather than specialised
tooling for which there might be less training data, so the models will be a bit less sure on how to use them.

This is also why I quite like to use API directly when using LLMs for investigations. (Yes, I do that
on an industrial basis these days, because we're all "AI first"). The models are quite good at figuring
out what to do on their own. I noticed that when I was using 
[ZAP with OpenAPI and MCP]({{< ref "/posts/make_it_so_genai_and_zap.md" >}}) a while back. These days I
tend to stay clear from MCP as the models seem to do a better job when they just have curl and schemas and
a few general instructions, so they don't always have to work from first principles.

There was one other bit in the post that made me think:

> Nobody puts "we built a really good rate limiter" on their landing page. But without fresh, queryable data, 
> your agent can't answer the question that actually matters: did I break this, or was it already broken?

The whole post was all about ingesting log data from GitHub, and building a good rate limiter enabled them
to keep querying github without hitting long pauses due to eating all the requests per minute.

# And finally

In a delicious piece of irony, I note that Sam Altman has called [the deal that OpenAI has entered with 
the Pentagon sloppy](https://www.theguardian.com/technology/2026/mar/03/openai-pentagon-ceo-sam-altman-chatgpt).

AI sloppy. Who'd have thunk it. No prizes for guessing who did write that contract.

# And finally finally

[This video](https://vimeo.com/1168468796?fl=pl&fe=sh) by the Norwegian Consumer Council explaining
enshittification is gold. I am still chuckling thinking about it.

That was the weakly link, goodbye.