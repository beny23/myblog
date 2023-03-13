---
title: "Why AppSec fails"
date: 2023-03-12
tags: ["appsec","agile","security","devsecops"]
featured_image: "/images/why_appsec_fails_title.jpg"
---

Let me tell you a story about Application Security (AppSec). It contains heroes and villains, and I'm not necessarily 
thinking about the defenders and attackers here. It contains lots of interesting technology that is often overemphasised. 
We've got whole industries that work on letting us know how scary it is out there, vulnerabilities that are marketed like 
rock stars and terminology that makes you quiver in your boots: who would want to fall victim to an Advanced Persistent Threat 
(APT)? There are red and blue teams and that make me think of the Matrix. But what's behind the jargon?

I've been working in AppSec for a while now, and got into it more or less [by accident]({{< ref "how_i_learned_to_stop_worrying_and_love_a_vulnerability.md" >}}). 
Rather than regurgitating the [Wikipedia definition](https://en.wikipedia.org/wiki/Application_security), I'm going to 
try to explain it in my own terms. And as such will try not to overwhelm you with jargon (although a passing familiarity
of software development is assumed). 

AppSec primarily deals with not ending up on the news.

# Stop the press!

How would your software end up on the news? Here are a few possibilities:
 
- You lose your users private details
- Your software stops working for hours or days and people notice and get annoyed (also, something about contractual
  obligations, Service Level Agreements, reputational damage)
- A security researcher finds a security hole and publishes their findings because you've ignored them or don't have
  a way of reporting it
- All your internal information is leaked to "threat actors" - no, not Liam Neeson and his particular skills, but 
  hackers, black hats, script kiddies, spies, organised crime gangs or school children. 
- After the horse has bolted, you get a big fine because you can't convince the Information Commissioner's Office 
  that your software security practices weren't negligent and ends up on the BBC.

# Cock-up not conspiracy

[Quoting Douglas Adams](https://en.wikipedia.org/wiki/Somebody_else%27s_problem) comes in handy here

> An SEP is something we can't see, or don't see, or our brain doesn't let us see, because we think that it's somebody 
> else's problem. That’s what SEP means. Somebody Else’s Problem. The brain just edits it out, it's like a blind spot.

It is really easy to create insecure code. A simple addition [can crash your system]({{< ref "one_plus_one_is_crash.md" >}}).
No honest developer puts these bugs in on purpose. So why do they do it?

## Ignorance is bliss

I am not trying to dunk on developers here (hey I am one), but I find security holes can often be introduced when you haven't come
across a particular issue before and therefore are not aware of the risks. One of the biggest challenges in AppSec is 
keeping up with the latest tactics, techniques and procedures (TTP).  There is a lot to learn and technology does not
stand still either. 

A useful starter is to keep up-to-date with the [OWASP top 10](https://owasp.org/www-project-top-ten/)
which describes the top 10 categories of security snafus. An even better way would be to chat with security people.
I find that having a slack channel called `#community-security` in your organisation (other messaging solutions are available)
is a great way to get lots of relevant security stories. Of course, that relies on the AppSec engineers being open 
about what they do and sharing interesting research.

## Developing/testing the happy path

"Works for me" makes me cringe a little, but at the same time, it makes sense. Developing software in an 
agile way often means we build a Minimum Viable Product (MVP) and then iterate on it. That shouldn't mean we forget 
about security though. If only the expected input and output (i.e. the "happy path") is considered, we're not going to 
find unexpected issues. And as Security issues are often related to making a system work in ways that are never intended 
to, it is important to foster a mindset of "how could I exploit this?" in the developer and testing community.

Looking at Test Driven Development (TDD) and really thinking about the systems that are being built is crucial
here. I'd recommend watching [Dave Farley's YouTube channel](https://www.youtube.com/c/ContinuousDelivery).

## Security as an afterthought

How often have you come across a situation where the team needs to build a new feature quickly, goes off and develops
something and then about two weeks before release an external penetration tester (pentester) is brought in to
check whether everything is secure. What is the issue there? Well, actually, what are the issues (plural)?

- External pentesters lack the context of what the software is meant to do. Sometimes I despair when pentesters come
  in, fire up Kali Linux and bring up lots of Critical issues that are related to an open port that ends up not being
  publicly accessible because they only tested the QA environment. And completely miss that it's possible to view 
  someone else's medical record. But that is not really the pentester's fault. It's not going to be possible read all the
  background documentation and understand all the source code when they're only hired for 48 hours.
- Two weeks before the go-live date, the pressures to release often mean that the decision is made to "accept the risk"
  and press ahead. Because it is too late in the day to fix it.
- Not only is it often too late to fix something, it's also going to be very expensive if design decisions need to 
  be reversed or production data migrated to a more secure option.

Treating security as an afterthought? Just don't do it. Modern software engineering is based on the principles
of the [Agile Manifesto](https://agilemanifesto.org) - and one of the most resonating principles for me is

> People over process

At the beginning of this piece I mentioned heroes and villains. 

Here's the first example:

> A Change Advisory Board (CAB) is convened to decide whether the new "Superduper" system can go live, but Bob the
> security architect is concerned that the Architecture Review Board has not given approval and he will not give sign off
> because the data storage solution is not part of the Enterprise Architecture blueprint.

Contrast this with:

> Alice is a developer and during the latest demo she noticed that an email address was part of the URL. She asks
> in the `#team-appsec` channel for advice. After chatting asynchronously and explaining that this application and emails was 
> only ever internal, the risk was assessed as low and the AppSec team was happy for that to go into production as is.

I hope it is quite obvious who the hero is here. It's Alice for asking for advice and not waiting for AppSec to find
the issue.

The point that I am trying to make is that securing systems is much more about communication and collaboration than 
it is about process and technology.

# But what about scale?

Scaling AppSec is hard. These anti-patterns come to mind:

- There is a central AppSec team that needs to approve all new technology and investigate every vulnerability that
  is found. That's just a giant bottleneck and will not work. 
- We add a security expert to every team. Because they're embedded, they have the context and can make decisions
  that will not impact the speed of delivery. That sounds nice, but in my opinion is also completely unrealistic because
  good security people are not easy to find and are expensive. If there are 50 delivery teams, you cannot afford 
  50 security experts.
- Let's just buy Ultimate Security Platform Plus! We will have lots of tooling that automatically runs Dynamic
  Application Security Tests (DAST) and Static Application Security Tests (SAST) and of course, you'll need
  Software Component Analysis (SCA) and every software engineer's development environment will be automatically hooked
  up, so nobody can push insecure software into production. Ever. Because we've defined a policy that blocks
  all CVEs with a score of 8.0 or higher.  Except that won't work either because lots of problems 
  [will still go through]({{< ref "curating_vulnerabilities.md" >}}) and the developers will get frustrated because
  they need to stop every 2 seconds to upgrade yet another library.

# So what now?

Security is about identifying and mitigating risk. In order to find the risk, it is really important to know where 
to look. As soon as there are more than a handful of services, it makes sense to collect information about patterns.
What libraries are used? How are they used? Where is the potentially problematic code or configuration. What risk can
be accepted?

These pattern libraries can be manually updated spreadsheets or shell scripts full of regular expressions or
automated tooling that helps to gather information. Even Ultimate Security Platform Plus! has its place.

AppSec is often about reading logs and finding indicators of possible issues. Threat hunting is a more snazzy way of 
saying trawling through logs. 

Against that background, I find the idea of the old D&D Alignment quite handy:

![AppSec Alignment](/images/why_appsec_fails_alignment.png)

- True positives are findings that indicate a problem. This can be as trivial as some PII leaked into internal
  logs (who has got access to those logs?) but could be as bad as an unauthenticated public endpoints that exposes
  the whole finance database.  Those are evil.
- False positives are a often a pain. When your SCA brings up 50 libraries that have Critical vulnerabilities with a
  CVE score of 10, only to find out that nothing actually uses that particular functionality can be a relief (good!), but also
  a big time waster (Chaos!).
- False negatives are very dangerous. Just because there's no evidence of a problem, does not necessarily mean
  the problem does not exist. It is worth challenging assumptions and revisiting issues. (Chaotic evil)
- True negatives are difficult to prove, but quite rewarding when finding that an exploit has been successfully mitigated
  (Lawful Good).

But that is going to be the next post...
