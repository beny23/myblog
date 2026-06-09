---
title: "Weakly Link 26/21: Control Failures"
date: 2026-06-08
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2026_21_title.jpg"
---

Apologies for the tardiness, when I started this set of posts, I squarely aimed to do this once a week. Of course,
I never fully committed to that, it was more of a weakly held belief that I would hold out. So, unfortunately dead
reader, I've failed. I didn't have enough control over my schedule and before I know it some weeks have passed. Let's
fix that.

In this edition we're looking at control failures.

# Start with a whopper

Brian Krebs had a rather [exciting story](https://krebsonsecurity.com/2026/05/cisa-admin-leaked-aws-govcloud-keys-on-github/) 
about a good demonstration of what happens when common sense leaves the building. A researcher found a GitHub repo
called `Private-CISA` which despite the name was public. Ouch. This is the kind of thing that security nightmares
are made of. The repository contained a collection of links, credentials, passwords and other sensitive CISA assets.
This gets better when you spell out what CISA stands for: Cybersecurity and Infrastructure Security Agency.

Yup, and it gets even better:

> \[..\] the exposed CISA credentials represent a textbook example of poor security hygiene, noting that the commit 
> logs in the offending GitHub account show that the CISA administrator disabled the default setting in GitHub that 
> blocks users from publishing SSH keys or other secrets in public code repositories.

I mean, who among us hasn't stored plaintext passwords in a repository and pushed it to the world to see? No?
Maybe slashing the budget for cybersecurity wasn't the greatest idea.

# Own goal

It's the soccerball world cup soon, we'll probably see plenty more own goals. Well, I didn't have to wait long.
Kevin wrote about how [Microsoft dealt with a security researcher](https://doublepulsar.com/microsofts-stance-on-zero-day-exploits-is-a-dumpster-fire-of-their-own-making-0946117940a4):

> Recently, somebody going by the name of Nightmare Eclipse has been having an online beef with Microsoft around 
> security vulnerabilities they claim they had been trying to report. Their posts read like those of a former 
> Microsoft employee. They’ve been dumping proof of concept exploits for said vulnerabilities publicly.

It seemed that nobody knew how to deescalate there. Publishing exploits for vulnerabilities publicly isn't great
but Microsoft seems to have not exactly covered themselves in glory either:

> Nightmare Eclipse was also kicked off GitHub (owned by Microsoft), Gitlab (a Microsoft partner), they 
> were doxxed on Twitter and had their MSRC — Microsoft vulnerability reporting portal — account disabled. 
> It’s quite difficult to ‘responsibly’ report future vulnerabilities when you have been banned.

Bit of double standards:

> GitHub has long been a source for zero days exploits in competitor products — it still is. While I worked there 
> GitHub had a policy saying they wouldn’t remove them. By continually removing just exploits for their own products 
> from Github and declaring “criminal activity”, it’s a rubicon you shouldn’t cross.

Criminalising and marginalising hackers (security researchers in corporate speak) sets a dangerous precedent,
and doesn't make much sense considering:

> They \[Microsoft] have a long history of hiring people, some with criminal convictions for hacking offenses — and 
> hiring people who’ve posted zero days publicly.

# When your Cloud Provider becomes the Supply Chain Risk

[The Register](https://www.theregister.com/off-prem/2026/05/20/google-cloud-suspended-major-customer-railwaycom-without-cause-causing-outage/5243111)
led me to an interesting incident report about an [outage at Railway.com](https://blog.railway.com/p/incident-report-may-19-2026-gcp-account-outage)

> Railway experienced a platform-wide outage after Google Cloud suspended services on our production account. 
> This took our API, control plane and databases offline, along with compute infrastructure hosted on Google Cloud.

Ouch. According to the incident the production account was disabled due to some automated action on Google's side. 
According to the timeline, it took only 20 minutes from Railway's monitoring to detect that something was amiss, 
to finding that the production account was disabled, to raising a P0 ticket with Google to getting the account 
reinstated.

What followed was a lot of work to restore the systems to a good state. There's an interesting lesson there:

> Railway’s network is a mesh ring, built up of high availability fiber interconnects between Metal <> GCP <> AWS. 
> However, in this ring, there was still a hard dependency on workload discoverability being tied to the network 
> control plane API that was hosted on the machines running in Google Cloud.

It's the same old story where multi-cloud meant that a single cloud provider could still take out the whole business.
Personally, I prefer simplicity! Why have the extra complexity of multi-cloud when it all comes tumbling down in
a heap anyway... Of course, that stance is defensible for most businesses, but I guess when you're running a PaaS
business

> Deploy, run, and manage applications in the cloud without dealing with infrastructure directly.

then usual rule of "don't piss about with multi-cloud, it'll cause more incidents than it'll solve" 
doesn't really apply. I just often despair at businesses that decide that their solution for stability problems
is multiple clouds when it really doesn't warrant the complexity.

# Long live XSS

This links us neatly to another story about complexity. How often do you find security pentest reports that
warn of XSS vulnerabilities and someone decides - "oh, that's just frontend, we can ignore that". Well,
Scott makes a great case for [definitely not ignoring 
it](https://scotthelme.co.uk/xss-is-deadly-for-passkeys-the-hidden-risk-of-attestation-none/):

> If malicious JavaScript can run on your page, it may be able to register an attacker-controlled passkey 
> against the victim’s account. The user sees nothing, the website records a successful registration, and the 
> attacker walks away with a valid authentication backdoor.

This is particularly devastating as passkeys are always touted as this phishing resistant, secure replacement
of passwords. The attack vector is worse than your garden variety XSS that exfiltrates session tokens, because
at least those are time-limited. 

Let's hope that everyone who is using passkeys is sending out email notifications that a new passkey was registered.
That might give us a bit of a chance to detect this attack. I recommend reading the post, because it also
gives a good intro of what goes on during passkey authentication under the hood.

# Ask and You Shall Receive

In our next story, we have another trust issue. We hear how [over 20,000 instagram accounts were 
stolen](https://www.bbc.co.uk/news/articles/c98rzr72dpyo) using nothing more than the strategy of "asking nicely":

> The activity involved chatting with Meta’s AI assistant, convincing it that the attacker was the legitimate 
> account owner, and tricking it into changing the associated email address.

Not verifying the email address seems a bit of an oversight:

> The tool itself worked properly and functioned as intended; however due to a bug in a separate code path, 
> the system did not properly verify that the email address provided by the individual requesting a 
> password reset matched the email address associated with that user’s Instagram account

Ummm, so the code worked as intended? Meta must be using a different definition of "working as intended".

# Working as intended

Talking of working as intended, I'm sure the following [Register 
story](https://www.theregister.com/ai-ml/2026/05/22/zuck-defends-monitoring-employees-to-win-ai-race-in-purported-leaked-audio/5245379)
is not how people are intend to work.

> Zuckerberg purportedly answered an employee's question about "device monitoring" with a six-minute monologue 
> in which he said Meta employees are very smart and to win the most competitive technology race in history, 
> he would need to collect their keystrokes, mouse clicks, and screenshots to make its own AI measure up to 
> its rivals.

WTF? Stop building the torment nexus!

That was the weakly link, goodbye.