---
title: "Weakly Link 26/06"
date: 2026-02-10
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_06_title.jpg"
---

This week, we're having a bit of a wild west theme to the GenAI related links. There's continuing
hype around OpenClaw - though it looks more like a hangover than a party. We've also got some interesting
use cases for GenAI that are directly not related to coding and we'll end up with a cryptic warning from
a siren.

# The Good

I'd like to start off with a couple of links relating what is feasible with GenAI:

First of all, we've got [GitHub Agentic Workflows](https://github.github.io/gh-aw/). The idea is simple,
instead of juggling YAML, you just write some friendly English text to specify what you want a GitHub
Action to do, and then it'll get done.

My first thought was, what a load of BS. Why would I swap out the determinism of clearly defined actions with
some wishy-washy text? But then I noticed that this the vagueness might actually be a feature. Looking
at an [example security workflow](https://github.com/github/gh-aw/blob/v0.42.13/.github/workflows/daily-malicious-code-scan.md?plain=1)

> Look for these red flags in the changed code:
>
> - Network requests to external domains not in allow-lists
> - Environment variable access followed by external communication
> - Base64 encoding of sensitive-looking data
> - Suspicious use of `curl`, `wget`, or HTTP libraries
> - Data serialization followed by network calls
> - Unusual file system writes to temporary or hidden directories

That's a lot of not clearly defined rules that would make it quite interesting to have an LLM look over
code on a regular basis.

Staying with the good, Mark's [post on LinkedIn](https://www.linkedin.com/pulse/ai-assisted-architecture-governance-case-study-justice-mark-craddock-ihmsf)
about [ArcKit](https://github.com/tractorjuice/arc-kit) peaked my interest. 

> ArcKit is a toolkit for enterprise architects that transforms architecture governance from scattered documents into a systematic, AI-assisted workflow for:
> 
> - Establishing and enforcing architecture principles
> - Analyzing stakeholder drivers, goals, and outcomes
> - Risk management (HM Treasury Orange Book)
> - Business case justification (HM Treasury Green Book SOBC)
> \[...]

The case study is particularly interesting as it provides some rigour around discovery work. And of course,
the headline figure of generating 10 comprehensive governance artifacts in just over 3 hours work as well
as finding a potentially show-stopping risk made me take notice. I suppose the question is who
is going to read all this work.

# The Bad

Now with hell freezing over (I have something positive to say about AI), I'm glad that my next few
links are normal service resumed. [Last week]({{< ref "weakly_link_2026_05.md" >}}) we had a look at OpenClaw.
It didn't take long for stories to emerge that basically should be a big stop sign about its usage from
a security point of view.

First we've got a report from Jason about 
[the industrial scale of OpenClaw malicious agent skills](https://1password.com/blog/from-magic-to-malware-how-openclaws-agent-skills-become-an-attack-surface):

> I described OpenClaw as a kind of Faustian bargain. It is compelling precisely because it has real access to 
> your local machine, your apps, your browser sessions, your files, and often long-term memory. That same access 
> means there isn’t yet a safe way to run it on a machine that holds corporate credentials or has access to 
> production systems.
>
> If you have already run OpenClaw on a work device, treat it as a potential incident and engage your security 
> team immediately. Do not wait for symptoms. Pause work on that machine and follow your organization’s 
> incident response process.

Two particularly fun malicious skills are:

- [MacOS malware](https://cyberinsider.com/341-openclaw-skills-distribute-macos-malware-via-clickfix-instructions/)

You don't still thank that Macs don't get viruses, do you?

- [1-Click RCE via Authentication Token Exfiltration](https://github.com/openclaw/openclaw/security/advisories/GHSA-g8p2-7wf7-98mq)

> Clicking a crafted link or visiting a malicious site can send the token to an attacker-controlled server. The 
> attacker can then connect to the victim's local gateway, modify config (sandbox, tool policies), and invoke 
> privileged actions, achieving 1-click RCE. This vulnerability is exploitable even on instances configured to 
> listen on loopback only, since the victim's browser initiates the outbound connection.

This one is fun because it can be exploited even when OpenClaw users properly secure their bots with networking
restrictions. Though I think egress protection will catch this one.

# The Ugly

Well, I say the Ugly, but I think it's quite a [nice write-up](https://www.sysdig.com/blog/ai-assisted-cloud-intrusion-achieves-admin-access-in-8-minutes) 
by Alessandro and Michael about how an attacker is leveraging LLMs to very quickly move from initial access to 
admin access. 

I found it interesting though that the reason the Sysdig team thought it was AI wasn't because it was
so good, but because it was rather stupid in places:

> Curiously, they included account IDs that did not belong to the organization: two IDs with ascending and 
> descending digits (123456789012 and 210987654321), and one ID that may belong to a real external account 
> (653711XXXXXX). This behavior is consistent with patterns often attributed to AI hallucinations, providing 
> further potential evidence of LLM-assisted activity.

# M&S incident parliamentary proceedings

Now, the next link is more than half a year out of date. In July the Business and Trade Sub-Committee 
[grilled the M&S chairman](https://committees.parliament.uk/oralevidence/16268/pdf/) about the cyber incident.
Makes for some interesting reading. This one is an interesting threat whereby your systems are down because
you did it yourself:

> In terms of lessons learned, which I think was where the
> question started, something that we would say to others is: make sure
> that you can run your business on pen and paper, because that is what
> you need to be able to do for a period of time while all of your systems are
> down-you having taken them down yourself for protection.

# Wyden Siren

To finish off, some more good news. Senator Wyden has indicated that 
[the CIA is up to no good](https://www.techdirt.com/2026/02/05/the-wyden-siren-senators-cryptic-cia-letter-follows-a-pattern-thats-never-been-wrong/).
No details, he's not been wrong yet.

> The pattern repeats. Wyden asks a specific question about surveillance. The intelligence community 
> answers a slightly different question in a way that technically isn’t lying but is designed to 
> mislead. Wyden calls them out. Eventually, the truth comes out, and it’s always worse than people assumed.

Oh joy.

That was the weakly link, goodbye.