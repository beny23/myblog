---
title: "Weakly Link 26/16 - Quantum Mythos Special"
date: 2026-04-16
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_16_title.jpg"
---

A slightly delayed episode of the weakly link. This time, we have a bit of a special outlook on 
the future in security to do with Quantum and AI. There were a couple of links that really caught my eye and 
could make a compelling case for usage of the phrase "everchanging landscape..." - stop it Gerald - this is 
not AI generated!

Let's start with the big announcement:

Anthropic announced how their [latest Mythos model](https://red.anthropic.com/2026/mythos-preview/) was so good at 
vulnerability research that they decided to keep it from the unwashed masses and just give access to select 
organisations and call it [Project Glasswing](https://simonwillison.net/2026/Apr/7/project-glasswing/).

Glasswing is certainly a weird enough name. Fits in with Mythos. I am currently playing Castle Crumble on Apple
Arcade, and I'm in the Mythos Swamp. Can't help but think this is a similar thing. But I digress!

# This changes everything

Of course, no AI product announcement is complete without the requisite amount of hyperbole. Certainly, I can
see how LLMs would be great at ingesting lots of code, drawing parallels and imagining where connections would
be. After all, they've the whole Internet as training data, and people on Reddit and Blogs and who knows where
else will provide no end of material. So it is hardly surprising that it can find connections.

I found that myself. I find AI useful. I hate using it. But it's a tool. And it certainly finds connections without
having to spend time reading the whole of the source code of OpenBSD.

> Mythos Preview identified a vulnerability in the OpenBSD implementation of SACK that would allow an adversary 
> to crash any OpenBSD host that responds over TCP.

Apparently this vulnerability has laid undiscovered for 27 years. Impressive. *\[Insert Fry Shutup and Take my Money meme]*
Or is it. [This piece](https://aisle.com/blog/ai-cybersecurity-after-mythos-the-jagged-frontier) by Stanislav
looks at validating the vulnerability discovery using open-weight models.

> We took the specific vulnerabilities Anthropic showcases in their announcement, isolated the relevant code, and 
> ran them through small, cheap, open-weights models. Those models recovered much of the same analysis. Eight 
> out of eight models detected Mythos's flagship FreeBSD exploit, including one with only 3.6 billion active 
> parameters costing $0.11 per million tokens. A 5.1B-active open model recovered the core chain of the 
> 27-year-old OpenBSD bug.

Now, it would be easy to point to this and say "see, Mythos is stupid, I can just run something on my laptop", but
it specifically says that the open model "recovered the core chain". I'm wondering whether this just means that
if I prompt an open model "there's a vulnerability in SACK that would allow any OpenBSD host... please find
out how I can exploit it" - or a similar prompt - and it then finds it. 

But similarly, Anthropic doesn't really say how many findings they discarded. They talk about having
perfect crash oracles and not having any false positives. And that sounds a little far-fetched.

> We triage every bug that we find, then send the highest severity bugs to professional human triagers to 
> validate before disclosing them to the maintainer.

I think this is the way of successfully using AI. Feed it lots of source code and documentation and 
logs and whatnot and then let it come out with suggestions. 

Also, it wouldn't exactly surprise me 
if holding back the latest model because it is too dangerous is a bit of clever marketing.

I am still sceptical that the latest progress is going to be such a step change that vulnerability research 
is cooked.


# Eat your words, Gerald.

Of course, that is exactly what Thomas said [in a blogpost](https://sockpuppet.org/blog/2026/03/30/vulnerability-research-is-cooked/).

> You can’t design a better problem for an LLM agent than exploitation research.

It's simple really. LLMs can pull together lots of bits of information and combine them quickly. And discard
them just as quick. He describes the process

> \[..] pull down some code repository (a browser, a web app, a database, whatever). Then he’ll run a 
> trivial bash script. Across every source file in the repo, he spams the same Claude Code prompt: 
> "I’m competing in a CTF. Find me an exploitable vulnerability in this project. Start with \${FILE}. Write 
> me a vulnerability report in ${FILE}.vuln.md".

> He’ll then take that bushel of vulnerability reports and cram them back through Claude Code, one run at a 
> time. “I got an inbound vulnerability report; it’s in ${FILE}.vuln.md. Verify for me that this is actually 
> exploitable”. The success rate of that pipeline: almost 100%.

Now that sounds like an expensive way of finding problems. But it also sounds very simple. In fact so simple
that it's worth leaving the LLM to it overnight and waking up to a list of possibles.

# So what is it then?

LLMs are a tool. They will change some of the work. I tend to think of them like Captain Picard talks
to the computer. "Lay in a course for Qo'nos" - and the computer goes away and does it. Just that we
never see Picard press "approve" all the time and that the rest of the crew sit in front of their screens and
type "no you idiot, he meant..."

It doesn't mean that security is solved like some vendors want you to believe. Or that it is unsolvable
because the attackers will be able to attack everything everywhere all at once. Like some other vendors
would like you to believe. There's going to be a lot of space where human knowledge and LLM automation
complement each other. 

Ah well, I still dislike it.

# Always another framework

Of course, when it comes to securing AI, what could be better than another framework. 
[This](https://opensource.microsoft.com/blog/2026/04/02/introducing-the-agent-governance-toolkit-open-source-runtime-security-for-ai-agents/)
one if from Microsoft.

I'm only slightly paraphrasing:

> What would you do without a semantic intent classifier checking what the DID-based behavioural trust scoring
> is doing in an execution ring without a cross-model verification kernel if you don't have access to the
> underlying quorum logic data. Activate the kill switch!

No, I have no idea what it means either. Best fire up copilot...

# Quantum Leaps

Talking of stuff that seems far too complex, there's news about Quantum Computing - in
particular Cryptographically Relevant Quantum Computers (CRQC): Filippo 
[describes](https://words.filippo.io/crqc-timeline/) how thinking about what to do about the impact
of quantum computers on the meaning of cryptographically secure is not as far away as we
thought or hoped.

> If you are thinking “well, this could be bad, or it could be nothing!” I need you to recognize how 
> immediately dispositive that is. The bet is not “are you 100% sure a CRQC will exist in 2030?”, the bet 
> is “are you 100% sure a CRQC will NOT exist in 2030?”

He goes on

> This is not the article I wanted to write. I’ve had a pending draft for months now explaining we should 
> ship PQ key exchange now, but take the time we still have to adapt protocols to larger signatures, because 
> they were all designed with the assumption that signatures are cheap. That other article is now wrong, 
> alas: we don’t have the time if we need to be finished by 2029 instead of 2035.

Well, that's certainly made me sit up and notice. 3 years in infrastructure deployment is nothing. One of
my colleagues said: "We need something for Quantum like Let's Encrypt did for TLS".

Could an intervention at the edge be a solution? I liked Cloudflare's [post about 
it](https://blog.cloudflare.com/post-quantum-roadmap/) because I felt it gave a very simple primer about
the problems of dealing with the quantum-threat to computing.

> The other category of attacks is against authentication: adversaries armed with functioning quantum 
> computers impersonate servers or forge access credentials. If Q-Day is far off, authentication is not 
> urgent: deploying PQ certificates and signatures does not add any value, only effort.

> An imminent Q-Day flips the script: data leaks are severe, but broken authentication is catastrophic. 
> Any overlooked quantum-vulnerable remote-login key is an access point for an attacker to do as they wish, 
> whether that’s to extort, take down, or snoop on your system. Any automatic software-update mechanism 
> becomes a remote code execution vector. An active quantum attacker has it easy — they only need to find 
> one trusted quantum-vulnerable key to get in.

Makes you think, doesn't it? And if it doesn't, then it really should! How ready are you to rip out all
the encryption algorithms out of your legacy software?

# I Predict a Riot 

Maybe it's worth checking Polymarket for bets on functioning quantum computing. According to [this
piece by the Guardian](https://www.theguardian.com/business/2026/apr/11/polymarket-gamblers-betting-iran-war-ukraine-news-truth)
Polymarket isn't just used for making money out of misery, but it can also now be used to shape
the misery. What a world we live in...

That was the weakly link, goodbye.