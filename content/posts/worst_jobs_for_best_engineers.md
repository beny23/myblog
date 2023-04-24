---
title: "Your Best Engineers Should Look After the Worst Systems"
date: 2023-04-23
tags: ["agile","legacy","you-build-it-you-run-it"]
featured_image: "/images/worst_jobs_for_best_engineers_title.png"
---

"Outdated tech stack and metaphorical gaffer tape holding together the code" - sound familiar? How about 
"We can't recruit, because nobody wants to touch this legacy stuff"? Yet banks and governments would stop working 
if mainframes were switched off.

Instead of outsourcing to the lowest bidder, maintenance is a job for experienced engineers.  Not "the short straw", 
brown field development can be more exciting than a feature factory. After all, the shiny code written today is the 
legacy code of tomorrow...

# What is Legacy Code?

Let's take a little detour about what legacy code is.

According to [Michael Feathers](https://amzn.eu/d/3F0aKPj), code without tests is legacy code:

> The main thing that distinguishes legacy code from non-legacy code is tests, or rather a lack of tests. [..]
> With tests, you can make things better. Without them, you just don’t know whether things are getting better or worse.

I would like to add a qualification to this definition:

* Code without tests *that you can trust* is legacy code.

I hope that it is obvious why I make this qualification. But too often have I seen code where tests are gamed to hit
a certain percentage of code coverage. Or implemented using a testing framework that makes it very difficult to actually make
changes to code because the tests restrict the implementation or require mocks that fall over when an implementation
detail is changed.

Why is it so important that software is testable? Because without testing, it is difficult to change the code. And it
is the ability to change the code that makes a difference.

There's actually another qualification. Let's change the above to:

* Code without tests that *anyone* can trust is legacy code.

It is important that a system can be tested not just by the people that currently run it but also by people that
will run it in the future.

This allows for a generalisation:

## Complex code is legacy code

If a system is not kept simple, then it is difficult to change. It has been far too often that I looked at a piece
of code and thought "which idiot wrote this" only to find out a few minutes later "oh, it was me". That is usually
a good indication that something has turned into legacy code or it is too complex to understand easily. That decreases
the ability to change it.

But how do systems become legacy? Let me ask the question another way:

## Is Software ever finished?

The short answer is: It never is. Which software is released at version 1.0 and then "Done"? 

I tend to chuckle with amusement when Agile teams spend a 3-hour workshop on teasing out the difference between
"Done" and "Done Done". Is it done when the developer finishes "the code"? Or when the pull request has been reviewed? 
or when the QA has signed it off. Or when it is ready to be released? 

Spoiler alert: there is *no* good definition of "Done" because as soon as you try, something will happen that will
require extra steps.

Note, I intentionally used uppercase Agile above, as this kind of obsession with defining "Done" tends to be inversely
proportional to understanding the meaning of the original [Agile Manifesto](http://agilemanifesto.org).

## But What Happens when the Project is Done?

Ah. The project. How often have you been part of a project team that had been stood up to solve a big challenge?
Whereby expensive consultants (yes, yes, I am a consultant, and I hope I'm not selling myself cheap) are drafted in 
to fix a problem. Those things end up having dreadful names such as "Project Overlord" or some other name that is meant
to inspire or give the team a sense of mission importance. 

Once the elite developers have come in and implemented 
"the solution", they ride off into the sunset to their next mission. The project team dissolves and the system is handed over
to the support team who will run the system. Preferably cheaply, because we spent lots of money on the Rockstar
Coders that implemented it.

Makes sense, doesn't it? You wouldn't want your best people dealing with the mundane, the "business as usual", the
boring maintenance tasks, would you?

# Creating a Legacy

Actually, I think this is a recipe for problems. This is exactly how [IT white elephants](https://www.theregister.com/2021/07/23/nao_govt_it_projects/) 
get created. If the team that built the software doesn't also run it, then a lot of 
system knowledge gets lost as soon as code is handed over to a different team.

And if that team has got such uninspiring names such as "Live Services" or "BAU Support" or "Maintenance Mode" how likely
is it to attract great developers? I would argue, not very. Instead, there will be a high churn of people. And it will
just keep getting worse.

![Maintenance Death Spiral](/images/worst_jobs_for_best_engineers_maintenance_death_spiral.png)

How does this happen in practice? Nobody intentionally sets out to create Legacy Code, but all that needs to happen
is that "the business" decides to shop around suppliers to maintain a system for which there are no immediate
requirements for change. Why not give the work to someone cheaper?

But what happens in reality?

- The existing supplier usually does not have much incentive to train their replacements
- The new supplier had to compete on cost, so might send in inexperienced engineers to make up the numbers
- Existing engineers with system knowledge are not likely to take a pay cut, so they move on
- It takes months/years for the new people to get to the level of understanding needed, by which time the system has stagnated
  and got a reputation for being expensive or risky to change

And once something is poorly understood and risky to change, then [bad things happen](https://abcnews.go.com/US/faa-monitoring-systems-high-level-after-notam-failure/story?id=96391067) 

# Why would a skilled engineer want legacy code?

So far I've laid out lots of advantages for an organisation to have skilled people maintain the systems. But I would
go one step further and argue that it also makes sense for developers or consultants to seek out to be
involved with legacy code.

After all the horrible things that I said about legacy code above, you are probably questioning my sanity at the moment,
but I think there is method in my madness.

## Software Metaphors

Software engineering is a complex process, and metaphors are often used to help describe it.

From the construction industry we've got:

- Greenfield development (starting an app or service from scratch)
- Brownfield development (adapting it)

Traditional wisdom is that greenfield is much more exciting, because developers can let loose all the latest and best
tools with the latest and best frameworks without being encumbered by legacy code.

Andy Hunt and Dave Thomas do not like this metaphor. They liken software development to
[gardening](https://www.artima.com/articles/programming-is-gardening-not-engineering). This certainly resonates:

> With a garden, there's a constant assumption of maintenance. Everybody says, I want a low maintenance garden, but the 
> reality is a garden is something that you're always interacting with to improve or even just keep the same.

But like any metaphor this also has weaknesses. With gardening isn't really considered a [team sport](https://medium.com/decoding-tech/software-architecture-metaphors-90992e9be5f4) 

So I'd like to paint another picture. I think of looking after legacy code a bit like the restoration of a cathedral:

![Cathedral Restoration](/images/worst_jobs_for_best_engineers_cathedral_restoration.png)

And here's why I think that:

## Plans are lost in the mists of time

Like an old building, it is quite likely that the documentation of legacy code is not in the best state. It might
be lost altogether or worse hidden somewhere in a system that is not easily searchable and intermingled with lots
of out-of-date docs. (I'm looking at you, Confluence!)

## Skills are no longer available

In our age of microservices, functions and cloud it often falls to the more experienced engineers to remember what
it was like to assemble a WAR file. So there is a certain amount of archeology to understand how systems hang together.

## They knew what they were doing back then

One of the biggest fallacies is to equate legacy code with rubbish. That's the same thing as saying that the Sistine
Chapel is useless, because it isn't made out of glass and steel.

![Sistine Chapel](/images/worst_jobs_for_best_engineers_sistine_chapel.jpg)

Rubbish isn't it.

In a similar way is it foolish to say that mainframes built on COBOL or J2EE monoliths are crap, just because they
are not made of the same shiny.

## It Needs Experts

Legacy Systems need experienced engineers to look after. It won't do to just throw lots of bodies at the problem.
Yet I think that working on legacy systems can teach us a lot. To succeed, it is necessary to:

* Unpick complex code
* Add tests
* Add observability
* Understand the side effects
* Carefully make changes to improve

And those are really valuable skills. It isn't something that you would learn if you churn out features on a
[platform that provide these things for free]({{< ref "making_software_quickly.md" >}}).

## Cathedrals need to change too

It would also be a mistake to think that legacy software does not need to change. There is always change.
Legislation, regulation or simply changing requirement often make it necessary to change existing software
rather than rewriting it. In fact, complete [rewrites are rarely a good idea](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/).

![Louvre](/images/worst_jobs_for_best_engineers_louvre.jpg)

Similarly, the Louvre Palace wasn't built with that iconic glass pyramid or the Mona Lisa inside...

# Conclusion

I'm going to sound terribly arrogant now, as I think I have gained a lot of the skills that make me the engineer
today by starting on an engagement that on paper sounded very boring:

* Lift and shift some applications from an on-prem data centre into the cloud

Initially we didn't even fix bugs that were in the exiting system. Over time as we learned to understand the system we 
were able to simplify where needed, replace small bits that needed fixing and adapt the system to make use of the 
modern platform it was running on. As a result we improved, made it leaner and cheaper to run.
And I learnt a huge amount in the process. 

So now I find myself raising an eyebrow when people say that they're not interested in working with legacy systems.
What do you think of legacy now?


