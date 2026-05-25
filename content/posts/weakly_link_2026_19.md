---
title: "Weakly Link 26/19: Local Escalations"
date: 2026-05-12
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_19_title.jpg"
---

It's been a busy few weeks in security. You wait for ages for a local privilege escalation vulnerability and
then two come along at the same time. We find out that just because it's rust, doesn't mean it's secure. GitHub
security was bypassed and Gemini scored a perfect 10. Let's dive in.

# Copy Fail

Copy Fail is probably something that if you've been remotely interested in security you couldn't fail to miss.
You copy? At it's core it is an impressive class of vulnerability that allows privilege escalation from a
user account to root. And it does that by abusing a crypto function of the Linux kernel that is not often used.

I'll reproduce the table from the [blog post](https://copy.fail) - LPE stands for Local Privilege Escalation:

|                     |TYPICAL LINUX LPE|COPY FAIL|
|---------------------|---|---|
| Race condition      |required|none|
| Per-distro offsets  |required|none|
| Reliability         |30–80% per attempt|100%, single shot|
| Affected window     |narrow kernel range|2017 → 2026 (9 yrs)|

What is equally impressive is that this vulnerability is quite stealthy:

> The corruption is transient, though. Nothing is written back to disk: once the page is evicted 
> (memory pressure, `echo 3 > /proc/sys/vm/drop_caches`) or the system reboots, the cache reloads clean 
> from disk and hashes match again. A forensic image of the disk shows the unmodified file.

So how much of a panic is it generating. Is it - and I'm only saying this because it's the industry standard
do I panic comparison - "the next Log4Shell"? Well, yes and no. It isn't something that is exploitable
remotely. But it does mean that if someone can get a foothold, lateral movement probably is a lot easier.

The fact that it is serious is underlined by the fact that Cloudflare 
[blogged](https://blog.cloudflare.com/copy-fail-linux-vulnerability-mitigation/) (or should that be bragged?) 
about how they mitigated it. If anything it gives a really good write-up of how the vulnerability works.

# Dirty Frag

Ok, so because having one universal local privilege escalation vulnerability is never enough, [a second
one came along](https://www.wiz.io/blog/dirty-frag-linux-kernel-local-privilege-escalation-via-esp-and-rxrpc).
This one is called Dirty Frag, and it doesn't even have a patch yet.

Again, an important class of bug in a second phase of an attack.

# Surely AI is the answer

So what to do about all this vulnerable code. In previous weeks we've cast doubt over the hype
that had been generated over the ability of Mythos to "change everything". Daniel (him off curl fame) confirms
that somewhat in this [blog post](https://daniel.haxx.se/blog/2026/05/11/mythos-finds-a-curl-vulnerability/)
by being a bit disappointed:

> The single confirmed vulnerability is going to end up a severity low CVE planned to get published in sync 
> with our pending next curl release 8.21.0 in late June. The flaw is not going to make anyone grasp for 
> breath. All details of that vulnerability will of course not get public before then, so you need to hold 
> out for details on that.

I do like that the blog post looks at two angles here, and I wholeheartedly agree with them both. Depending
on whether you're an AI doomer or an AI booster, this is good or bad:

> My personal conclusion can however not end up with anything else than that the big hype around this model so 
> far was primarily marketing. I see no evidence that this setup finds issues to any particular higher or 
> more advanced degree than the other tools have done before Mythos.

And conversely bad or good:

> But allow me to highlight and reiterate what I have said before: AI powered code analyzers are significantly 
> better at finding security flaws and mistakes in source code than any traditional code analyzers did in the past. 
> All modern AI models are good at this now.

# Let's move on to the next panacea

Anybody who knows anything in security will tell you that it is outrageous that in 2026 we're still dealing
with memory safety issues. I mean, the Rust programming language with its borrow checker has been out for a while
now. So what are we even doing, still writing Operating Systems, Drivers and Software in silly old
C(++)? Are we mad?

[uutils](https://uutils.github.io) is a rewrite of lots of core unix utilities in Rust. So it would make sense
to adopt it, right?

Well, maybe not. This [seclists post](https://seclists.org/oss-sec/2026/q2/332) points to quite a large number
of issues that make the Rust coreutils implementation rather less safe than more safe. I guess writing
software is hard, and security is more than just memory safety.

I guess, we're only too human to hope that we'll get that one true way of magically fixing security.

# Magic fixes you say?

I wonder how much of infrastructure and code runs on GitHub these days. So when the wiz kids (sorry) found
[an injection vulnerability in GitHub's internals](https://www.wiz.io/blog/github-rce-vulnerability-cve-2026-3854)
it made me sit up:

> By exploiting an injection flaw in GitHub's internal protocol, any authenticated user could execute arbitrary 
> commands on GitHub's backend servers with a single git push command - using nothing but a standard git client.

This is a great reminder for the fact that securing systems is difficult because of how untrusted input often
turns into trusted input just because it is an internal system. Simple and scary. Good thing everyone who is running
GitHub Enterprise Server has patched it.

> GitHub Enterprise Server customers should upgrade immediately - at the time of this writing, our data 
> indicates that 88% of instances are still vulnerable.

Oops. 

What is interesting is that this research was made possible by GenAI.

> By leveraging AI-augmented tooling-particularly automated reverse engineering using IDA MCP-we were able to 
> do what was previously too costly.

Not because a human couldn't have done it, but because it was actually economical to get an AI to try to do
reverse engineering at a scale.

# AI Security Research

So, 10 out of 10 for AI? That's good right? Weeell, not when it's CVSS 10. Take [this interesting bug 
report](https://github.com/google-github-actions/run-gemini-cli/security/advisories/GHSA-wpqr-6v78-jr5g) on
gemini cli:

> In previous versions, Gemini CLI running in CI environments (headless mode) automatically trusted workspace 
> folders for the purpose of loading configuration and environment variables. This is potentially risky in 
> situations where Gemini CLI runs on untrusted folders in headless mode (e.g. CI workflows that review 
> user-submitted pull requests).

Potentially risky is doing a lot of heavy lifting in there. Basically, if an untrusted user was able to 
submit a PR which includes a `.gemini/settings.json` in a workflow that includes `uses: google-github-actions/run-gemini-cli`
then, gemini cli enabled a full supply-chain compromise.

# So what to do?

I stumbled across this excellent repository containing the [Systems Approach books](https://github.com/SystemsApproach).
The problems we've talked about above are generally problems of interactions of components. Like in the
wiz GitHub RCE, it was all about messing up the boundaries of trusted and untrusted input. 

Will I read it? Nah, I'll just point copilot at it and tell it to learn its lessons. That's how research works these
days, no?

# Research

To finish off a [lovely story](https://shkspr.mobi/blog/2026/05/ive-found-just-the-right-paper-for-my-bottom-hole-problem/)
by Terence about doing some "important" research which is to find out which real paper was use to create the fake
newspaper in an episode of Bottom.

Turns out, that there is a huge amount of information that isn't digital. So ChatGPT wouldn't have found it:

> So I contacted the fine people at Elmbridge Museum who were happy to rummage through their microfiche for me. 
> I expect, much like Indiana Jones, the archivists had to knock down fake walls, find a mystic box containing 
> the treasure, and then dodge various snakes and villains to retrieve the priceless artefact. Or they may have 
> a well designed archival system which is a pleasure to use. I don't know.

I guess it is important to remember that so much of human information is not digitised but is hiding away
in dead-tree format or on microfiche. I wanted to add a punchy reference to the Library of Congress only having
1% of its content digitised, but alas, copilot failed me, so you're just going to have to trust me on this...

That was the weakly link, goodbye.