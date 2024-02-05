---
title: "Resisting compliance is futile"
date: 2024-02-05
tags: ["my-take-on", "agile","compliance","appsec"]
featured_image: "/images/my_take_on_compliance_in_modern_dev_title.png"
---

About two months ago I stumbled across a great YouTube video of a talk by [Charity Majors](https://twitter.com/mipsytipsy) called
[Compliance standards should be modern development practices](https://www.youtube.com/watch?v=tuunGZ-4wPQ). Now let's
step back for a minute. Am I seriously suggesting that anything with the word "compliance" is going to be a riveting
watch? Why, yes I am. And with good reason. I'm a fan of [good security]({{< ref "why_appsec_fails.md" >}}) and I like
[agile]({{< ref "appsec_loves_agile.md" >}}). And I think one of the major stumbling blocks about putting Sec into DevOps is to
forget the agile origins of DevOps culture.

![Comply or Die](/images/my_take_on_compliance_in_modern_dev_comply_or_die.png)

This is where books like [Investments Unlimited](https://itrevolution.com/product/investments-unlimited/) or 
[Wiring the Winning Organisation]({{< ref "my_take_on_wiring_winning_org.md" >}}) come into their own. And this talk by
Charity at FinTech DevCon about compliance, standards and software development fits right in.

There a few points that stuck in my mind:

> Everyone tests in production, only some actually admit it

Modern software development is all about fast feedback loops. Getting into production as quickly as you can, allows you
to find problems quicker and therefore fix them quicker. How often have we had to learn that all bugs cannot be prevented?

I've been in waterfall organisations that spend months writing detailed requirements specifications, high-level and
low-level design documents, test strategy documents, test plans using enough paper to burn a good chunk of rainforest. 
And when we went into production, we found something didn't work properly, and we had to pull all-nighters to get the
system up and running.

The bad old days, you might say. Nobody does that anymore, right? Right?

But I've also been in agile shops - or shops that think that they are agile - where deployments happen often 
and something falls through the cracks and isn't discovered for months on end. Until the regulator comes along and
drops a huge fine.

Charity's comment challenges us to be honest. There is only so much faulty behaviour that we can observe in 
testing environments, and I like the idea of being honest about testing in production. Once we accept that, the next
few steps are logical:

First, "you build it you run it". That's the idea that

> * You can't operate the code if you didn't develop it
> * You can't develop the code if you don't operate it

A development team whose code causes regular middle-of-the-night alerts that wake up the Ops team won't get fixed with
the same urgency if it's *you* that gets woken up at 4am. And an Ops team that doesn't know how the code work, won't be
able to diagnose faults as efficiently as the people who wrote the code. Well, in theory anyway. All too often have I
looked at a snippet of code and thought "who wrote this shit?" - inevitably to be followed by "Oh, it was me".

![Facepalm](/images/my_take_on_compliance_in_modern_dev_facepalm.png)

Second, when consciously testing in production, it is important to build observability into your software. You need to be able
to look at your service and check whether there's anything weird going on. Or unexpected. And as co-founder of 
[honeycomb.io](https://honeycomb.io/), Charity has forgotten more about Observability than I will ever learn, so when 
she states that fast feedback loops are important and that continuous deployments make things safer, I would
trust that. In any case, the [Accelerate book](https://itrevolution.com/product/accelerate/) has shown how gates make software worse.

# But but, SOC2 and ISO 27001

Compliance is all about the triad of confidentiality, integrity and availability. You must protect customer data, you
must have policies and follow them. Frameworks such as SOC2 and ISO 27001 do not prescribe *how* to do that.

Too often people I've heard:

> We'd love to work agile or test in production, but we can't because of regulation!

It's just not true. Talk to your risk team. Find out what the frameworks actually say!

If the paperwork and the processes becomes more important than the security, then that is security theatre. And that
drags us down and makes us less secure. If our engineers constantly have to think about compliance, then we're doing
something wrong. [Tanya Janca](https://twitter.com/shehackspurple) did a [great talk](https://youtu.be/-ZxY2XlM3-0) 
about how slowing developers down artificially leads to worse security.

Engineering performance should be on everyone's mind. Anything that stops us from moving to production quickly, means
we lengthen our feedback loops. 

> Undeployed software ages like milk

Another great quote. The longer we leave software undeployed, say because of "change freezes", the bigger the changes
get when we finally open the floodgates. Ben Conrad did a great piece about 
[why change freezes don't work](https://www.linkedin.com/pulse/how-change-freeze-can-create-risk-ben-conrad/).

# There's no I in Team

I also found myself nodding very vehemently when Charity talked about teams. It's teams that make great software,
individuals may be talented or very experienced, but unless a team works well together and pulls in the same direction, it
will not work. 

To put it another way "great teams make great engineers". I've observed this many a time. Places where people have
worked well together end up delivering software at an unbelievable pace, and bringing junior engineers in those teams
lifts them to the same level. I've been on teams where after a year being part of it, every member could go out and step
up to a tech lead role no problem. And I've also seen toxic teams, where politics and infighting led to a death by 1000 
cuts.

Social-technical team performance so much more important than individual performance. The ability to ship quickly is 
informed by the team you are in, not your individual skill. In a good team shipping code should be as small, common, 
regular, unremarkable as a heartbeat. 

# Conclusion

I enjoyed the talk and find it very encouraging that from so many different places, I find similar messages about working 
in an agile fashion, where DevOps and DevSecOps means bringing the right culture. Peter Drucker said "culture eats
strategy for breakfast". [Gene Kim](https://twitter.com/RealGeneKim) stressed the importance "social circuitry". 
[Amy Edmondson](https://twitter.com/AmyCEdmondson) taught us about the importance of psychological safety. 
Example after example of "people over process", so let's make sure we treat compliance the same way. 
Anything else is futile! 

![Resistance if futile](/images/my_take_on_compliance_in_modern_dev_resistance_is_futile.png)

That last sentence was maybe a bit tongue in cheek, I certainly don't subscribe to the idea that it's all YOLO and 
we can throw out all policies and process documentation, but process and policy needs to support people doing their
work, not the other way round.