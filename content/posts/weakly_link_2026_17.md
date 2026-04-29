---
title: "Weakly Link 26/17: AI stutters"
date: 2026-04-26
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_17_title.jpg"
---

This week we're linking together links that give a bit of a picture of some stuttering
in the AI world. We've got Firefox overhyping Mythos. We've got indications that GenAI
vendors think they need to show some way of putting the right numbers on the balance
sheet and look at simpler times. Both in the past and in the future. Let's dive (no not delve) in.

# Days not numbered

We start with a look at a [Firefox blog post](https://blog.mozilla.org/en/firefox/ai-security-zero-day-vulnerabilities/) 
that caught people's imagination. While typically saying that something is "not hackable" or that "zero days are 
numbered" is probably not the best idea from a security point of view (because undoubtedly the BBSes are full 
of chatter that will lead to people trying to break in - ok, Gerald, wrong millenium, what's it these days 
"InstaTok"?), Firefox managed to set off quite some chatter with their assertion.

> As part of our continued collaboration with Anthropic, we had the opportunity to apply an early version of 
> Claude Mythos Preview to Firefox. This week’s release of Firefox 150 includes fixes for 271 vulnerabilities 
> identified during this initial evaluation.

> As these capabilities reach the hands of more defenders, many other teams are now experiencing the same vertigo 
> we did when the findings first came into focus. For a hardened target, just one such bug would have been 
> red-alert in 2025, and so many at once makes you stop to wonder whether it’s even possible to keep up.

Well that quite some claim. And the numbers - 271 vulnerabilities - are rather high. So do we need to take
it seriously. Well, maybe not quite. I found [an interesting teardown post](https://xark.es/b/mythos-firefox-150)
by Antide that puts things into perspective:

> The interesting question is not whether Mythos found bugs. It clearly did. The interesting question is what 
> kind of bugs were found, how serious they were, and whether those findings actually change the balance between 
> defenders and attackers.

The post goes through the publicly available commit history to identify what actually was fixed and finds that
the Firefox assertions maybe just ever so slightly overhyped. I liked that teardown, and also that the conclusion
finds its mark:

> - as a defensive assistant, Mythos looks credible;
> - as evidence of a dramatic offensive breakthrough, the Firefox case is still weak;
> - and as usual with AI security announcements, the most interesting part is hidden in the operational 
>   details we do not get to see.

# Trouble in Anthropical Paradise

But the trouble for GenAI doesn't stop there. [Simon runs a test](https://simonwillison.net/2026/Apr/16/qwen-beats-opus/) 
on new models that ask it to create an SVG of a Pelican. This is a clever tests that checks whether models 
can figure out what shapes makes up the bird. Antropic's latest Opus 4.7 apparently is not quite as good as 
Alibaba's Qwen 3.6 *running on his laptop*.

I guess that's not great for Anthropic's bottom line, when the open weight model outperforms what you charge
good money for. 

Thought probably not as embarrassing that Anthropic's new super security model itself didn't prevent someone
gaining access that shouldn't have. And because it never rains but it pours, that unauthorised access was
even reported on [the Beeb](https://www.bbc.co.uk/news/articles/cy41zejp9pko). There's a rule of thumb in
InfoSec that goes something like "you don't want to end up on the BBC" because that's when the brown stuff
has truely hit the spinny stuff.

# Show Me the Money

But security is not the only issue in LLM land. It all seemed to kick off when Anthropic decided to [remove
Claude Code from the Claude Pro subscription](https://www.wheresyoured.at/news-anthropic-removes-pro-cc/) and 
then reversed it. Then there was [news](https://github.blog/changelog/2026-04-20-changes-to-github-copilot-plans-for-individuals/) 
and [discussion](https://github.com/orgs/community/discussions/192963) about GitHub tightening the screw
in its Individual Plans:

> New rate limits are part of this change, including further restrictions on individual rates. We’re also 
> adding more transparent rate-limit tracking, so it’s easier to see when you’re approaching your limits.

And it wasn't just pricing that changed:

> Opus-family models have been removed from Copilot Pro. Rate limits that would be required to keep Opus 
> sustainable on Pro would result in a worse overall experience than using a capable model without those 
> limits. Users would likely hit their cap very quickly.

That does smell like someone needs to show profitability. Good thing nobody thinks that there's an
AI bubble...

Of course, the GitHub Copilot enterprise plans [followed suit fairly 
swiftly](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/#h-the-bottom-line).

Though I am wondering at the economic hoops that are being jumped through to be able to assert:

> Plan prices aren’t changing. You’ll have full control over what you spend, tools to track your usage, 
> and the option to purchase more AI Credits if and when you need them.

In other words, it's going to get more expensive...

# Copilot. But Which One?

While talking about copilot, it is only fair to mention [this gem of a 
post](https://teybannerman.com/strategy/2026/03/31/how-many-microsoft-copilot-are-there.html) that
tries to answer a fairly simple question.

> How many products does Microsoft have named 'Copilot'?

At the time of writing it was 81. By now, who knows. That won't be confusing at all.

I'm imagining a Microsoft Marketing Exec coming into the engineering department every 2 days and saying:

![Needs more copilot](/images/weakly_link_2026_17_cowpilot.jpg)

# Those were simpler times

Sometimes I think it's just a bit much. I thought this [story about 
tractors](https://wheelfront.com/this-alberta-startup-sells-no-tech-tractors-for-half-price/)
was a perfect metaphor:

> There’s a reason the used tractor market has been so robust. Plenty of operators looked at a $300,000 
> machine full of sensors and software and decided a well-maintained older unit was the smarter bet.

Reminds me of the [printer joke](https://programmerhumor.io/programming-memes/mr-printer/). And I also
think the same way about cars. Give me mechanical buttons over [distracting touch 
screens](https://group.mercedes-benz.com/technology/digitalisation/connectivity/microsoft-collaboration.html) 
any day!

# Simpler things for a simpler future

Now, that does not mean that older is better. But simplicity ever more important!

[Brian's post about laziness](https://bcantrill.dtrace.org/2026/04/12/the-peril-of-laziness-lost/) is
a good reminder that keeping it simple:

> Of these virtues, I have always found laziness to be the most profound: packed within its tongue-in-cheek 
> self-deprecation is a commentary on not just the need for abstraction, but the aesthetics of it. Laziness 
> drives us to make the system as simple as possible (but no simpler!) to develop the powerful abstractions 
> that then allow us to do much more, much more easily.

And in terms of getting rid of old complexity requirements, I'm pleased to see the 
[NCSC recommending passkeys](https://www.ncsc.gov.uk/news/ncsc-leave-passwords-in-the-past-passkeys-are-the-future).
One of the main benefits according to the advice is just that it is secure with less friction.

> The majority of cyber harms to individuals start with criminals stealing or compromising login details, 
> making the adoption of passkeys a huge leap in boosting the UK’s resilience to phishing attacks.

It's also good to know that

> passkeys are at least as secure as, and generally more secure than, pairing the strongest password with 
> two-step verification (2SV).

And (at least on the Mac - don't know about Windows these days), it's been really convenient to just
keep pressing the fingerprint scanner to login just about everywhere.

Now if only we could have accounts without passwords!

# Shut up Breaches!

Another week, another breach. This time it was Vercel that leaked customer secrets. But in an interesting
way:

> A Vercel employee installed an AI tool called Context.ai. It asked for Google Workspace access. They clicked 
> Allow.
>
> That grant became the door.
>
> Not a zero-day. Not a phishing email. Not a nation-state attack on Vercel's infrastructure. Context.ai was 
> compromised by an attacker, who used the OAuth access Context.ai held on behalf of Vercel employees to gain 
> delegated access to their Google Workspace accounts, and from there, to Vercel's internal systems.

When was the last time you reviewed what OAuth access you give away?

# Nowadays you can't even trust the Internet

Now a quick interlude into the North Korean job market. 

> HexagonalRodent primarily targets Web3 developers with the goal of stealing crypto assets. They achieve this 
> via social engineering developers with the promise of high-paying tech jobs, a technique that has likely 
> become more successful as a result of consistent mass layoffs in the industry and the resulting hiring glut. 
> The threat actors may reach out to targets directly via platforms like LinkedIn, or publish fake job openings 
> to popular career portals.
>
> Once the threat actors have lured in a developer with a fake job offer, they then request that the developer 
> undergo a coding skills assessment. \[..]
> 
> HexagonalRodent’s skills assessments are subtly backdoored with malware. One technique often leveraged is the 
> tasks.json feature in VSCode (an extremely popular code editor). The config file enables developers to configure 
> automated tasks to be run by VSCode when certain events occur. The threat actors abuse this by shipping their 
> own tasks.json with a malicious runOn:”folderOpen”command configured. This causes VSCode to execute malware 
> simply as a result of the target opening the source code folder with VSCode.

Not that this is a new thing, but apparently it needs an AI angle:

> However, the initial loader for the BeaverTail and OtterCookie malware did, at one point, contain artifacts 
> consistent with AI-generated code. The comments are overly verbose, including literal step-by-step 
> explanations of what each fragment does, as well as a slew of emojis.

Verbose comments and detailed explanations as a sign of compromise! Now that's funny!

# Letting the Cat out of the Bag 

Last not least, a view into the organisation that are responsible for Internet engineering standards.
I'm glad that not content with providing engineering context on [Teapots](https://www.ietf.org/rfc/rfc2324.txt),
[IP over Avian Carriers](https://datatracker.ietf.org/doc/html/rfc1149) or even via 
[Drone](https://datatracker.ietf.org/doc/html/draft-zhixin-tech-ip-oac-drone-00) now also brings
us something about [cats](https://www.ietf.org/archive/id/draft-meow-mrrp-00.html). 

Now that is a Useless Use Of Cat ;-)

That was the weakly link, goodbye.