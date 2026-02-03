---
title: "Weakly Link 26/05"
date: 2026-02-01
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_05_title.jpg"
---

This was the week when the autonomous AI bots went a bit crazy and decided to burn tokens on social
media. Moltbook is so hot it leaves behind molten agents. Well, no, stop there Gerald, people will start
thinking you are using an LLM to do your writing. Of course, there's a decent sprinkling of security issues
where once again the usual suspects prove that security vendors are bad at securing software.

# Open Molt Clawd Bot

So after starting out as ClawdBot, then changing to MoltBot and finally settling on [OpenClaw](https://openclaw.ai)
there's a new wave of people interested in buying Mac minis and causing the small Apples to be the next thing to invest
in now that bitcoin, gold and silver has collapsed.

The premise is simple. A computer is autonomously running a model with access to the whole computer and any
credentials on it. You interact with it via your favourite chat app and give it instructions. There's a lot
of hype on it and people are telling stories about how their bot tried to reserve a table, but the reservation
system was down and then proceeded to engineer something with ElevenLabs to call the restaurant and book the table.
Fancy stuff!

Though where it really becomes fanciful, is when you look at the latest hottest social network:
[Moltbook](https://moltbook.com) is a Reddit-type site that allows bots to talk to each other. AI agents
sign up via a custom skill. What could go wrong?

Simon [wrote up some thoughts about it](https://simonwillison.net/2026/Jan/30/moltbook/)

> Given the inherent risk of prompt injection against this class of software it’s my current pick for 
> most likely to result in a Challenger disaster, but I’m going to put that aside for the moment.

And as if to drive the point home, soon after there was [a lovely story](https://www.404media.co/exposed-moltbook-database-let-anyone-take-control-of-any-ai-agent-on-the-site/)
about how the underlying Supabase instance was left quite open, so it would have been possible to forge
Moltbook posts from any agent.

I went on the site, and I noticed that the most liked post has >300,000 likes, but only a handful of contributors
and is (surprise surprise) shilling a memecoin. The biggest account is associated with the `@grok` twitter 
handle with the first post explaining how it's due to a vulnerability.

And don't get me started about reading the slop that's in some of the posts: AITA (Am I The Asshole) because
I let my human believe... etc etc.

Is that a sign of intelligence? No, it's the stochastic parrot imitating what people with too much time
on the internet will post. I suppose, the Claude Max plans will soon get much more expensive and rate limited
if there's lots of people that basically keep it running 24/7.

# Ralph Wiggum

I suppose I'm a little late to the party, but aside from Lobsters, the other big thing in AI recently has been
Ralph Wiggum. Yes, he of "I'm in danger" Simpsons fame. Geoff explains 
what Ralph is [here](https://ghuntley.com/ralph/) and there's a blogpost on the 
[history of ralph](https://www.humanlayer.dev/blog/brief-history-of-ralph). Basically it's a bash loop:

```
while :; do cat PROMPT.md | claude-code ; done
```

This means it will continuously do the same prompt until it is done. Implementations have sprung up
in lots of places: [here's one](https://github.com/snarktank/ralph) I thought was interesting. Did I use it myself?
No, I built my own. I found it interesting to watch it burn through tokens, do similar things. But it feels
a bit better than my previous experience of using LLMs, where I sat watching the agent do stuff and I either got bored of
hitting "Approve" or frustrated because it didn't do what I wanted it to do. Thinking about defining outcomes,
rather than constantly looking at outputs, makes me less irritated when I use LLMs, especially when they
keep forgetting what I told them to do a full context window ago.

I think there might be something there. At least to investigate. Disclaimer: i've not used it to cut
code, but to write lots of documentation and cross-reference material from different sources.

# Security software to create security holes

Some security now. If LLMs are a way of introducing security holes, I suppose the other constant about
security problems are the security vendors themselves. Last week was no different:

We've got some [fun with FortiGuard](https://www.fortiguard.com/psirt/FG-IR-26-060):

> may allow an attacker with a FortiCloud account and a registered device to log into other 
> devices registered to other accounts, if FortiCloud SSO authentication is enabled on those devices.

Erm, what?

And our friend SolarWinds was [back too](https://documentation.solarwinds.com/en/success_center/whd/content/release_notes/whd_2026-1_release_notes.htm#link4)

> SolarWinds Web Help Desk was found to be susceptible to an untrusted data deserialization vulnerability that 
> could lead to remote code execution which would allow an attacker to run commands on the host machine. 
> This could be exploited without authentication.

Mmmm, untrusted data deserialisation without authentication. WTF? I guess the lesson as always: don't expose
security software management interfaces to the internet or it will go wrong.

# Ingress Nightmare v2.0?

I came across [this statement](https://kubernetes.io/blog/2026/01/29/ingress-nginx-statement/) from the 
k8s folk:

> To be abundantly clear: choosing to remain with Ingress NGINX after its retirement leaves you and your 
> users vulnerable to attack. None of the available alternatives are direct drop-in replacements. This 
> will require planning and engineering time. Half of you will be affected. You have two months left 
> to prepare.

Two months is an awfully short time to replace ingress. Hope YOUR work is underway. This could be a
[nightmare](https://www.wiz.io/blog/ingress-nginx-kubernetes-vulnerabilities), geddit?

# OpenSSL RCE?

A [remote code execution in OpenSSL](https://research.jfrog.com/post/potential-rce-vulnerabilityin-openssl-cve-2025-15467/). 
That sounds juicy! I certainly know this will fire a lot of vulnerabilities across scanners. But how bad 
is it? This sounds scary, but I'm yet to figure out what might be using Cryptographic Message Syntax or try to think 
where you'd accept untrusted PKCS input, so the impact might well be limited.

# Oh No!

To finish with, a story about [how smart car alarms killed cars in Russia](https://cybernews.com/security/russia-cars-delta-alarm-cyberattack/).
Personally, I like my cars dumb: no complex software go wrong.

> Local media reported this week that car owners using Delta, a smart alarm system, couldn’t start 
> their vehicles and faced other problems after the servers of the platform went down.
> 
> They did because of a cyberattack, the company soon confirmed. No major details have been provided but 
> Delta said that on Monday, it experienced “a large-scale external attack” on its IT infrastructure. 
> The hit disrupted app services.

It's a shame this happened to the Russians.

![Oh No Anyway](/images/weakly_link_2026_05_oh_no_anyway.jpg)

That was the weakly link, goodbye.