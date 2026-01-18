---
title: "Weakly Link 26/03"
date: 2026-01-18
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_03_title.jpg"
---

This week's installment of my LinkBlog covers old tech that is new, operational tech that is secure, observability
that is not expensive and a series of vulnerabilities for us to snigger at, then take a breath and take seriously.

# Mainframes are not dead

I have [often said]({{< ref "/posts/worst_jobs_for_best_engineers.md" >}}) that learning COBOL is on my bucket list 
and that my advice for young people in the software engineering sector is that learning how to program 
mainframes would be quite profitable as the current cohort of greybeards retire, and nobody understands 
anymore how the thing that holds banks and insurances and government together actually works. Learn COBOL, 
it's the future!

Against that background I thought it rather useful to stumble across this 
[free resource](https://www.ibm.com/products/z/resources/zxplore) by IBM on how to explore their Z-series
mainframe platform. And far from being an outdated thing that nobody uses anymore, it is still going strong.

I found the following stat quite interesting:

> Get hands-on experience to learn in-demand IBM Z skills to help build a career in enterprise computing, 
> which powers 68% of all worldwide transactions.

I can well believe that. Though I'm sure the statistic, like any statistic, could be spun to saw something
completely different and/or put out of context. Like I'm doing... The point is, I think it's an interesting
place to learn because just because something is old, doesn't mean that it's crap.

# But it's probably expensive...

Expensive, probably yes. I found this [blog post](https://usetero.com/blog/the-question-your-observability-vendor-wont-answer) 
from Ben quite illuminating about a different money sink:

> You run observability at your company. But really, you're the cost police. You wake up to a log line in 
> a hot path, a metric tag that exploded cardinality. You chase down the engineer. They didn't do anything 
> wrong, they're just disconnected from what any of this costs.

The long and short of it is that this is a really great question:

> How much of my observability data is waste?

Do you know it? I know that there a tons of DEBUG logs that nobody every needs. Except for that one time 2 years
ago when we had that incident and it would have been useful which is why we added it afterwards. Now it's just
creating noise and security incidents.

> Put it to the test. Ask your vendor what percentage of your data is waste. They'll play ignorant. 
> "It's your data." They don't understand it well enough to tell you what's worth keeping. But they 
> understand it well enough to sell you an AI SRE that can "root cause in minutes."

Yes, great questions. Something to think about when designing observability pipelines. Don't get me wrong,
I'm not advocating NOT logging, but it has to be done smartly. And to do that, it needs the engineering
culture to think about these things. 

# Culture? What about security culture?

The kids from Wiz had a great week. This [blog post](https://www.wiz.io/blog/wiz-research-codebreach-vulnerability-aws-codebuild)
details how a simple missing regex anchor could have been used to pwn the AWS console. Let that sink in:

> Just two missing characters in a Regex filter allowed unauthenticated attackers to infiltrate the build 
> environment and leak privileged credentials. This post breaks down how we leveraged this subtle 
> misconfiguration to achieve a full repository takeover, and provides key recommendations for CodeBuild 
> users to harden their own projects against similar attacks.

The blog post is a really interesting journey on how existing filtering mechanisms in AWS's own libraries
could have been tricked into accepting PRs from GitHub users that were not connected to AWS. Sure, it wasn't
a simple process, involving timing attacks, generating thousands of GitHub accounts to get one with the right
ids, but it demonstrates the kind of things that can be achieved. I love reading about these exploits, because
the outside-of-the-box thinking it demonstrates to exploit the simplest of misconfigurations is really cool.

# Talking about supply chains

Considering the level of uncertainty about the US engagement with cybersecurity - where US officials have
been [instructed to miss security conferences](https://www.nextgov.com/people/2026/01/trump-officials-consider-skipping-premier-cyber-conference-after-biden-era-cyber-leader-named-ceo/410744/)
for petty reasons. And [uncertainty](https://www.darkreading.com/cybersecurity-operations/can-global-decentralized-system-save-cve-data) 
about whether NIST will be able to run CVE program, it probably is a bit of good news that there's now 
a [European alternative](https://gcve.eu/2026/01/07/gcve-db-announce/). Though who knows, maybe Trump will put
tariffs on that too.

# Ha! Ha!

In this week's "let's point the finger and laugh" moment is provided by the fact that 
[breachforums got breached](https://www.darkreading.com/threat-intelligence/breachforums-breached-exposing-324k-cybercriminals).

It's almost as if you can't trust criminals...

# More humour

Last but not least, I'd like to draw your attention to the [NCSC's guide](https://www.ncsc.gov.uk/collection/operational-technology) 
on securing operational technology (OT). OT is the software and networks that run factories and production lines.
That's the kind of thing where cyber attacks can be quite deadly.

I am really quite glad though that whoever put it together has a wicked sense of humour and writes in 
[the examples](https://www.ncsc.gov.uk/collection/operational-technology/worked-examples/ot-data)

> Admin Corp runs a plant that produces Adminox, a highly volatile, refined form of administrative paperwork 
> that is essential to every organisation in the country. It is created from volatile raw products using a 
> continuous chemical process.

\[..]

> The process for producing Adminox involves a number of steps, with the final product stored under pressure 
> in a tank. Clearly, no one would benefit from the unconstrained release of this Adminox, with the potential 
> for additional red-tape clogging up local services for years.

That was the weakly link, goodbye.