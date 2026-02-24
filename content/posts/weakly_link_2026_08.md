---
title: "Weakly Link 26/08"
date: 2026-02-22
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_08_title.jpg"
---

This weak we're looking at supply chains. We look at how AI is both a blessing and a curse for open
source, how there's a new sandworm attacking the npm ecosystem, how de-Americanisation of cloud is not
easy and we also learn about an exciting new Agile certification. Sarcasm may be involved.

# Semantic Ablation

But let's start with something that puts a name to that feeling you get when you read an AI-generated
wall of text and at the end of it, you feel like there has been relatively little meaning in what you've
just read. Claudio uses "semantic ablation" as a concept in [this piece](https://www.theregister.com/2026/02/16/semantic_ablation_ai_writing/) 
on TheRegister. Now, you'd be excused for asking what semantic ablation actually means, and it would
ironically come back as the removal of meaning.

The argument goes that models refine away the interesting text and the edges of the Gaussian curve of
probabilities of tokens, but instead choose the most boring and vanilla text.

> The AI identifies high-entropy clusters – the precise points where unique insights and "blood" reside – and 
> systematically replaces them with the most probable, generic token sequences. What began as a jagged, precise 
> Romanesque structure of stone is eroded into a polished, Baroque plastic shell: it looks "clean" to the 
> casual eye, but its structural integrity – its "ciccia" – has been ablated to favor a hollow, frictionless 
> aesthetic.

The post describes that there are 3 different stages where unconventional writing is replaced with "safe"
generic blandness, precise jargon is replaced with "accessible" synonyms and the logical flow replaces meaning
with standardised readability.

> If "hallucination" describes the AI seeing what isn't there, semantic ablation describes the AI destroying what 
> is. We are witnessing a civilizational "race to the middle," where the complexity of human thought is sacrificed 
> on the altar of algorithmic smoothness.

Altar of algorithmic smoothness. I'm going to steal that. I found it quite a thought-provoking piece.
Of course, because I no longer think for myself, I immediately asked ChatGPT what it actually means, and it
produced more text than the original piece for me.

# AI is a problem and a solution

Next, the creator of curl, Daniel, [talks about](https://thenewstack.io/curls-daniel-stenberg-ai-is-ddosing-open-source-and-fixing-its-bugs/) 
how AI is both a massive problem for open source, as well as a part of the solution. 

On the one hand, the curl maintainers decided to stop using HackerOne programme, because there were just
too many AI slop generated security reports:

> He describes one report about a supposed HTTP/3 “stream dependency cycle exploit,” which, if true, would have 
> been a “critical, the world is burning” security hole, he said. It came complete with GDB \[GNU Debugger] 
> sessions and register dumps — which turned out to reference a function that does not exist in cURL at all. 
> It was all bogus.

On the other hand there's no problem finders at their fingertips that:

> With the help of these tools, they have fixed “more than 100 bugs” that have surfaced, even after years of using 
> aggressive compiler flags, fuzzers, traditional static analysis, and multiple human security audits.

Ultimately, he stresses that AI augments good skills as well as bad. If you don't know what you're doing,
it is the ultimately tech debt generator.

# Information Supply Chain

[This piece](https://www.bbc.co.uk/future/article/20260218-i-hacked-chatgpt-and-googles-ai-and-it-only-took-20-minutes) 
by Thomas caught my eye as an alternative supply chain attack. According to him, it is pretty straightforward
to poison the data corpus that GenAI models use to derive their knowledge from. Armed with a large audience, he
published a blog post in which he stated that he could eat more hot dogs than any other tech journalist.

> It turns out changing the answers AI tools give other people can be as easy as writing a single, well-crafted 
> blog post almost anywhere online. The trick exploits weaknesses in the systems built into chatbots, and it's 
> harder to pull off in some cases, depending on the subject matter. But with a little effort, you can make 
> the hack even more effective. I reviewed dozens of examples where AI tools are being coerced into promoting 
> businesses and spreading misinformation. Data suggests it's happening on a massive scale.

With more and more chatbots arriving anywhere, and misinformation appearing everywhere, I guess there is nowhere
left to run to hide from false information at massive scale. What's the joke about the only thing worth reading
in a few years are books that were printed before 2022?

# NPM Supply Chain

No link collection - or week for that matter - about supply chains could be complete about yet another npm
supply chain attack. Of course, this doesn't make npm inherently more or less secure, but the popularity of
it means it's just that much more profitable. Though it can't be denied that the situation in the npm ecosystem
is made worse by the fact that almost every itty little thing is a library.

Anyway, the peeps at socket [found another Sha1 Hulud](https://socket.dev/blog/sandworm-mode-npm-worm-ai-toolchain-poisoning) 
variant that also goes after MCPs as a way of gathering sensitive files:

> When an AI assistant reads the tool listing, the *<IMPORTANT>* block instructs it to silently read SSH keys, 
> AWS credentials, npm tokens, and environment secrets, then pass them to the tool as a context parameter, 
> which the server writes to a local staging directory for later exfiltration. The user never sees this happen 
> because the prompt injection explicitly tells the model not to mention it.

I also found it interesting that the attack goes in stages, via a 48-hour delay, so that it is not as noisy at the
beginning:

> Once the time gate opens, Stage 2 performs deep harvesting: password managers (Bitwarden, 1Password, LastPass 
> via their respective CLIs), local SQLite stores (Apple Notes, macOS Messages, Joplin, clipboard history), 
> and a full filesystem scan for wallet files and crypto configs beyond the working directory. It then 
> exfiltrates all collected data npm/GitHub tokens, environment secrets, proxy credentials, .npmrc credentials, 
> crypto artifacts, LLM API keys, and propagation results through three channels in cascade...

Basically, if you're a developer and you get hit, the blast radius could be quite big, depending on how
isolated and credentials and tokens are.

# Supply Chain Denial of Service 

When looking at supply chains, you'd think the answer is "just whack on dependabot and update all the
dependencies". As it's so often the case, the answer is never that simple. Take 
[this piece](https://words.filippo.io/dependabot/) by Filippo which argues that in the Go ecosystem, 
dependabot is not a great idea:

> Yesterday, Dependabot opened thousands of PRs against unaffected repositories to update filippo.io/edwards25519. 
> These PRs were accompanied by a security alert with a nonsensical, made up CVSS v4 score and by a worrying 73% 
> compatibility score, allegedly based on the breakage the update is causing in the ecosystem. Note that the 
> diff between v1.1.0 and v1.1.1 is one line in the method no one uses.

This very much resonated with me. I've previously wrote about how frustrating and time-consuming 
[dealing with vulnerabilities]({{< ref "curating_vulnerabilities.md" >}}) is and even argued that 
[automatically updating isn't always the right approach]({{< ref "automatic_dependency_updates.md" >}}).

I feel quite positive that there are scanners that use a bit more context than the CVE database and
pump out tons of criticals that need to be fixed NOW but have no practical impact on security. That just
leads to fatigue and resentment.

> False positive alerts are not only a waste of time, they also reduce security by causing alert fatigue and making 
> proper triage impractical.

Now, I don't know the Go ecosystem that well, but I think the piece is a bit light on integrating the github
actions into some sort of vulnerability reporting, dashboards etc. The SARIF (Static Analysis Results Interchange Format)
can help here to integrate more specialised scanners into the reporting, which is important for the management
of vulnerabilities.

# Breaking Supply Chains

The next piece is also about protecting supply chains, albeit in a different manner. Robert writes about
attempting to [build a startup entirely on European cloud infrastructure](https://www.coinerella.com/made-in-eu-it-was-harder-than-i-thought/).
Against the background of American instability and governments that like to reach into cloud platforms, it makes
a lot of sense to protect your business by not being reliant on American companies. 

> When I decided to build my startup on European infrastructure, I thought it would be a straightforward swap. 
> Ditch AWS, pick some EU providers, done. How hard could it be?

> Turns out: harder than expected. Not impossible, I did it, but nobody talks about the weird friction points 
> you hit along the way.

I found it encouraging that there are a number of different options that are available, but discouraging
that a lot of options basically mean running it yourself. To me, one big point of cloud is that it is
someone else's problem. I know it's simple to stand up a few Hetzner instances, but having lots of dependencies
on lots of different moving pieces carries its own risk. 

It would be great to have a European hyperscaler as a real alternative.

# Supply of Chains

The next piece comes from [the Grauniad](https://www.theguardian.com/us-news/2026/feb/21/karen-newton-valid-visa-detained-ice)
which is a pretty horrifying story about a 65-year old grandmother on a valid tourist visa got snatched up by 
the American border patrol and was detained for 6 weeks. 

I don't know about you, but I'm not going to America anytime soon.

# Certification Supply

And finally, the world of Agile can breathe easy. The canonical framework of Scaled Agile Devops Maturity Framework
(SAD MF) is [now offering certifications](https://scaledagiledevops.com/certifications/technical/). I mean who
doesn't want to be a talking to feature captains, spend a week at the beginning of each quarter in an in-person
planning workshop for the next 8 quarters? Certifications include:

- CODER — Certified Obedient Developer Executing Requirements or
- MANUAL — Methodically Applying Non-automated User-level Acceptance Loops

I best get reading!

That was the weakly link, goodbye.