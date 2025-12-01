---
title: "Weakly Link 25/48"
date: 2025-11-30
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_48_title.jpg"
---

This week is an odd mixture about sandworms, supply chains and basically how everything is 
broken anyway. And then (after all the naughtiness) let's end on something nice.

# Thumped by a Sandworm

Sha1 Hulud came back for another go at supply chain attacks. Patient zero of this wave appears
to have been Posthog - an analytics platform a bit like Mixpanel. According to their 
[postmortem](https://posthog.com/blog/nov-24-shai-hulud-attack-post-mortem), their build pipeline
was compromised by a simple pull request. As an open source project, they are (rightly) proud
of accepting community contributions, but unfortunately, they missed something which gave the
attackers a foothold and allowed them to compromise their npm repository account. Well worth a read
as it goes into great detail about how their build pipeline was compromised and how easy it was
to misunderstand something that then gets exploited by attackers. And things happen quickly. From
exfiltration of the secrets to pushing of the poisoned packages was just 12 hours.

# Golden Oldies

Posthog does recommend that thing to look at is [minimum release age](https://docs.renovatebot.com/key-concepts/minimum-release-age/)
this prevents the bots that are doing the supply chain updating (like renovate) from picking packages
that have only just been published. I'm a bit conflicted by this. Doesn't this mean that we're 
relying on someone else to find the problem and have our fingers crossed that they find it before
the minimum release age clock ticks away? What if everyone had this set up, and more and more
vulnerabilities slip through the cracks because everyone is waiting?

Granted, zero-days where it is crucial to update libraries in a matter of days are going to be rare,
especially as I believe there's plenty of software shops out there that would take quite a while
to get the right paperwork together and run it through a CAB or side-step change freezes before fixing
something. So waiting a little bit before pushing a brand-new release into your codebase is probably
a good thing. So much so that I've previously argued that 
[automatically updating dependencies]({{< ref "automatic_dependency_updates.md" >}}) might not be the best idea.

# This is odd

It shouldn't really be a surprise that a lot of the supply chain attacks come through NPM. Not only
is Javascript (and Typescript) rather popular, it also has the strange obsession with having libraries
for everything. Who else do you explain that the `is-odd` package has 
[600,000 weekly downloads](https://www.npmjs.com/package/is-odd) and the underlying source code
has been archived over 5 years ago [i-voted-for-trump/is-odd](https://github.com/i-voted-for-trump/is-odd).
Good name though. Voting for Trump is odd...

# Who encrypts the encrypter?

[This story on the BBC](https://www.bbc.co.uk/news/articles/c62vl05rz0ko) made me chuckle. A crypto firm
loses an encrypted key and can't run their elections. How adorable. I've not changed my stance on crypto,
it is a dumpster fire that is all too often used for fraud. But having encryption keys that can be 
irrecoverably lost is probably not the best place to be in. How we all laughed, but let's be honest,
if you lost your phone and the MFA keys on it, have you got a backup? If the key vault in your cloud
provider gets lost in a fire, would you be able to recover all the PII data that you so diligently
encrypted? Some food for thought there. And what's the associated risk of backing up the keys to the
kingdom?

Onto something less doom and gloom.

# MCP UI

I thought this was an interesting announcement. [MCP UI](https://blog.modelcontextprotocol.io/posts/2025-11-21-mcp-apps/)
or MCP apps, looks to make it easier for different user interfaces to be presented. To be honest
I didn't find the demo to be that impressive. It embedded a few graphics into the chat interface.
Maybe prompt injection wasn't big enough a security problem. Having UI components being brought
in... what could go wrong. I mean, I think I'll stick with giving the LLM models access to my
command line for now ;-)

# Laughing out loud material

I really liked [this rant](https://functional.computer/blog/programming-peaked) about how programming 
has peaked. When we think about it, we know have computers that are several orders of magnitude
facter when it comes to raw computing power, and we still have to wait for things to load,
there's lag on webpages or programs and the UX is truely terrible (MS Teams, is that you?).

Samir reflects on what's changed since 2015, but go back another 30 years, and there was 
[GEOS](https://en.wikipedia.org/wiki/GEOS_%288-bit_operating_system%29) on
the C64, which managed a GUI, copying and pasting between applications, WYSIWYG word processing and a
paint app. With 64KB of RAM.

Glorious. It was just as slow as Word sometimes is now on my Mac with 32GB RAM. That's only about 500,000
times more...

![GEOS](/images/weakly_link_2025_48_geos.jpg)

# Advent

Let's close with [Advent of Code](https://adventofcode.com) which is kicking off again in a few hours.
This year, there's only going to be 12 challenges, so less stress. But I'm still unsure what language
to pick. Maybe Fortran could be a laugh!

That was the weakly link, goodbye.