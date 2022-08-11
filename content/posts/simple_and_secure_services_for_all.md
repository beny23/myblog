---
title: "A mission statement that actually makes sense"
date: 2022-08-11T16:40:01+01:00
tags: ["agile","digital-platform","paved-road"]
---

I was really positively and pleasantly surprised when I found out what HMRC Digital's mission statement was. 

Normally, I am not a fan of mission statements. They usually read like this:  

> Our mission is focused on six core aspirations the company continually strives to achieve...

Or some such drivel. Verbal gymnastics to make a company sound like everything to everyone - without being offensive
to anyone - which then gets used to "align" people on mandated fun days.  The bigger the organisation, usually the 
worse it gets, usually more long-winded paragraphs with long words that emphasise complexity to ascribe lofty virtues
to... you get my drift.

So I was really delighted when I had occasion to ask about HMRC Digital's mission statement. HMRC is the tax collecting
arm of the UK government.  The statement reads:

# Simple and secure services for all

That's it! Nothing about customer engagement, tax payer value, empowering citizens or digital transformation.  Really
just simple and to the point. And I love it! 

It is no-nonsense and offers a real glimpse behind the philosophy of the great teams that own and operate the 
Multichannel Digital Tax Platform (MDTP).  I've written plenty about MDTP previously: [here]({{< ref "making_software_quickly.md" >}}),
[here]({{< ref "catalogue_deepdive.md" >}}) and [here]({{< ref "curating_vulnerabilities.md" >}}).

Let's unpack the mission statement:

![Mission Statement Grid](/images/simple_and_secure_services_for_all_grid.png)

Now, let's look at this grid in more detail:

## User

For a user of the service (the taxpayer) it means:

### Simple

Digital services are easy to use.  They follow the [GOV.UK Service Standards](https://www.gov.uk/service-manual/service-standard),
are built using the [GOV.UK Design System](https://design-system.service.gov.uk), which means a consistent and
intuitive user interface driven by user research that makes interacting with government as simple as possible.

### Secure

The user data is secure and no more data than needed is collected

### All

The site usable by all. This doesn't just mean providing Welsh translations or being accessible (though very important
in itself), it also means a lot of thought has to go into services to ensure the right content is displayed to guide the
user when filling in potentially very complex forms.

## Service Developers

For the service developers it means:

### Simple

Digital services are [easy]({{< ref "making_software_quickly.md" >}}) to build. Services developed on MDTP follow a 
paved road that make it easy to integrate with the GDS standards above.  Prototype kits support designers, libraries and build pipelines support 
service developers and observability is built in as standard and a [service catalogue]({{< ref "catalogue_deepdive.md" >}})
makes it possible to find your way around.  

Due to the opinionated nature of MDTP, developers
can move between teams without having to relearn a whole new tech stack, which is quite important when operating
at a scale of over 1,000 microservices.  Incidentally, about 120 new microservices were added to MDTP in just the first
six months of 2022, a testament to how easy it is to develop on MDTP.  

Granted, being opinionated means that not 
all the latest "shiny" toys are available and Scala is not the most popular language, but it works really well for
this use case.

### Secure

The opinionated approach makes it possible to [centralise some aspects]({{< ref "curating_vulnerabilities.md" >}}) of 
application security (AppSec).  Centralisation also makes it possible for automation to be a feasible investment.  It also 
allows security engineers to try to find without having to have security specialists built into every team.  

### All

MDTP has built a very inclusive platform where it is easy to find help and get support.  At the core of is people,
most people I've met on MDTP are eager to help and support each other.  Numerous times have I seen questions asked on
a platform team channel that then gets answered by someone not from that team but happens to see the message.  The
[catalogue]({{< ref "catalogue_deepdive.md" >}}) allows people to find a Slack channel to ask questions quickly and
Slack itself is used to very good effect.  

## Platform

For the platform engineers/owners it means:

### Simple

MDTP is built on AWS. But MDTP opinions mean that not all of AWS's 17 ways of running a container are available.  In
fact to a service developer, AWS is abstracted away.  There are platform services that are built on AWS, but service
teams can't access the underlying infrastructure directly.  This keeps the architecture simple and consistent across
services.  We all know that using Kafka and CQRS would look good on an engineer's CV but is not necessarily value
for money.  A platform that only deals with Mongo is easier to maintain that a different DB vendor for each service.

Simplicity is key and means that 150 people can look after the platform and makes automation and self-service possible.
The price (and very worth paying in my opinion) is that it's not possible to run any old payload on the platform.  
Digital services must be built in a certain way.

### Secure

A dedicated platform security team monitors the security of the platform, working with application security to keep 
threats out.  Secure here also dips into reliable, whereby services are self-healing and don't require human intervention.
This is also true of accessing servers or data stores.  MDTP does not want humans to log into containers - not to fix 
things and not to diagnose.  If access to raw data is absolutely required, then service teams can build services for
administrative purposes.  They can then be tested and properly secured by the service teams themselves.

### All

People on MDTP come from all walks of life and there is a sense that
everyone is pulling in the same direction regardless of which supplier they come from or whether they're permanent staff,
apprentices or contracting.

# Conclusion

There is a lot packed into a simple mission statement.  As coders will agree, the simpler the code, the more elegant.
Keep it simple and stay agile.  Very powerful.

When I started writing this post, I had an ending in mind.  Some quip about loving the statement but still not seeing
the point of "mission statements".  But the more I think about it, there's something nice and inspiring about
packing the aims and goals of an organisation into a simple sentence!