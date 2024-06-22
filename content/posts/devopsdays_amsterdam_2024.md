---
title: "DevOpsDays Amsterdam 2024"
date: 2024-06-21
tags: ["devopsdays", "conference", "agile", "devops", "generative-ai"]
featured_image: "/images/devopsdays_amsterdam_2024_title.jpg"
---

Once again, I'm sat at [Schipol Airport]({{< ref "devops_enterprise_summit_2023_wednesday.md" >}}) typing up my thoughts
about a few days of refreshing Dutch DevOps Goodness! Ok, I admit one or five alcoholic beverages might have been 
involved too. This was my first [DevOpsDays](https://devopsdays.org/events/2024-amsterdam/welcome/) and it did not 
disappoint.

![Gerald and Pakhuis](/images/devopsdays_amsterdam_2024_pakhuis.jpg)

My day started at 3am to catch a flight at 6am but because I was still awake at 1:30 and had decided sleep is for wimps,
I arrived a little bit wired. Nothing a great coffee from the [Coffee TukTuk](https://www.koffietuktuk.nl) couldn't 
sort out. In a way, this first experience of arriving slightly after the conference had already started (for 
some reason the border checks back at the airport was being done manually) and resetting myself for a busy few days by
taking some small steps involving a very agile (coffee) delivery method would make a great metaphor of what was to be
ahead.

> By the way, if you found the last paragraph a little bit convoluted, I still refuse to use AI for writing my blogs,
> even though we couldn't stop talking about it at conference. More on that later.

![Agile coffee delivery](/images/devopsdays_amsterdam_2024_coffee_tuktuk.jpg)

# Workshops

Wednesday was workshop day. For my first one, I went to see [Rushil talking about how Google does SRE](https://devopsdays.org/events/2024-amsterdam/program/ws-rushil-sharma/)
and while ~~Site~~ Service Reliability Engineering was not new to me, it was a really great introduction to all the 
different service level indicators, objectives and agreements. And I very much liked how Rushil gave a live demo and
challenged the demo gods to make his site fail. Well, he also used a load testing tool to make the site unresponsive.
Brave! And great to watch when it all (intentionally) went wrong!

![SRE](/images/devopsdays_amsterdam_2024_sre.jpg)

# Rockstar Developers

Next, I felt like a rockstar as I was ushered into the little studio and did an interview with Joep talking about
my "Love Letter to Legacy" [talk]({{< ref "worst_jobs_for_best_engineers.md" >}}).

![Green Room](/images/devopsdays_amsterdam_2024_green_room.jpg)

# Legacy is the new black

Next on the menu was a really fun workshop where [Wouter and Suzanne](https://devopsdays.org/events/2024-amsterdam/program/ws-wouter-lagerweij-suzanne-lagerweij/) 
gave us a preview of his new [Escaping Legacy](https://escapinglegacy.com) book. The premise is simple:

- in order to extricate yourself from legacy code, you have to understand the old system
- too often that can only achieved by mapping out the system and (re)creating tests
- heat maps, story maps and example mapping can help.

We got some hands on experience with the concepts, which were made easy because we had to put some stickies in
order, made somewhat easier by the stickies having numbers on them. Laziness is a virtue!

I actually got an early version of the book from Wouter a few months, and really really enjoyed it, so grab it, I 
found it a great read even if you're not the eponymous product owner!

# AI

Next, we had a [terrific trio of googlers (Rania, Fahd and Christiaan)](https://devopsdays.org/events/2024-amsterdam/program/ws-rania-mohamed-fahd-ekadioin-christiaan-hees) 
talk to us about how AI is going to change everything in the software lifecycle. I have to admit, that I did a bit
of sceptical sniping, but as we got talking after, I could see my resistance toward GenAI soften a little bit. The example
on how a model was used to ingest 40 years of PubMed research and produced diagnostic suggestions with references to 
papers to be used by human clinicians is quite a compelling argument that there is something to it.   

It's not the first time that AI got a mention. I did rib them ever so slightly when they asked during their presentation
they asked "what does AI stand for?" and I was first off the mark with "Apple Intelligence" as the obvious choice.

I still think that "slap AI on everything" is not the right solution, but it has its uses. Later on during the conference,
when I was talking to sponsors, I had an interesting demo from Elastic where they showed off their capability to create
log ingesting pipelines using a few snippets of English...

# Getting butterflies

On day 2, I started the day by finding a spot that looks the perfect place for where Netflix must have come up with
the concept of the paved road:

![Paved Road](/images/devopsdays_amsterdam_2024_paved_road.jpg)

It is a beautiful location, where you can place your laptop at a desk, sip a coffee and contemplate two levels of 
guard rails, before you crash into the drink. Obviously I've totally made it up, but this is now my new go-to
picture when I want to describe the "golden path"...

# The real reason for conferences

While I'm being slightly irreverent, the following snap illustrates the real reason for conferences:

![Food](/images/devopsdays_amsterdam_2024_food.jpg)

And the food was amazing!

# Can you tell us a bit more about the talks though?

I really enjoyed [Sara's keynote](https://devopsdays.org/events/2024-amsterdam/program/keynote-sara-gerion) about 
meritocracy and glass ceilings in tech. One idea that was quite eye-opening to me was the thought that successful 
people are often successful because of the opportunities that they can grab. The comment that 

> if women displayed the same assertiveness as men in talking about their achievements, they face backlash, if they
> don't then they risk being ignored

made me want to stand up a shout "smash the patriarchy and glass ceilings". And I feel like that not just because
I'm a father to my daughter, but also recognise that diversity is strength. Grrr, I can feel my blood boil just
thinking about it. And it's even worse that discrimination is everywhere. As a middle-aged, middle-class white bloke,
I feel it only right to be angry about it, even if I've not experienced it myself.

# DevEx

Next up was a cool [talk by Jeremy](https://devopsdays.org/events/2024-amsterdam/program/jeremy-meiss) which reminded
us about the road we've been on in terms of IDEs:

![IDEs](/images/devopsdays_amsterdam_2024_ides.jpg)

Obviously everyone has their own favourite tool stack(s), his reasoning for choosing nano as his editor of choice was
that he wanted an editor that he could quite without throwing the computer out the window. At the risk of rekindling
a religious war, this is how you exit vi, right?

```
>>> what is the most obscure way of exiting vim?
One of the most obscure ways to exit Vim is by entering the 
command mode and typing in `ZZ`. This command saves and 
exits the file, similar to `:wq`.

>>> how do you enter command mode?
You can enter command mode in Vim by pressing the Escape key.
```

I can just feel myself being replaced by AI already - the above was created using the `phind-codellama` local model...

And I also really enjoyed [Coté's talk](https://devopsdays.org/events/2024-amsterdam/program/cote) about developer 
productivity. One reason I just have to applaud is the use of ["that McKinsey diagram"](https://tidyfirst.substack.com/p/measuring-developer-productivity)
but using it and giving it sensible meaning. There's no use trying to optimise the developer build cycles or trying
to optimise how fast someone can "type teh codez", when usually there is so much hand over time wasted when going from
development to QA to security to operations. That's where the real focus should be. Reduce the hand-offs, remove the silos.
Couldn't agree more!

![McKinsey waste](/images/devopsdays_amsterdam_2024_mckinsey_waste.jpg)

One of Coté's proposed solutions is to go to the metrics buffet and try to find ways to incorporate Dora and Space to make
devs happier. One of the most interesting slides measures the adoption of CI/CD, which as it turns out is pretty flat.
That was surprising, I thought it was obvious that DevOps principles and agile development are the way forward
in software development. I've experienced when it was working [really well]({{< ref "making_software_quickly.md" >}})
and also how frustrating it can be when it's not working well.

![Flat CI/CD](/images/devopsdays_amsterdam_2024_flat_cicd.jpg)

Maybe we shouldn't be surprised. I know I heard a lot about flat hierarchies, holocracies, psychological safety,
autonomous teams. I preached it too. But the reality is that letting go of control is a scary thing to do and requires
an enormous amount of trust. And trust is something that can easily be lost, and hierarchies re-established. And it 
was somewhat sad but also a bit reassuring that not everyone works in an autonomous, psychologically safe nirvana ;-)

# Ignite Talks

The ignite talks by [Robin](https://devopsdays.org/events/2024-amsterdam/program/ig-robin-gilijamse),
[Daniel](https://devopsdays.org/events/2024-amsterdam/program/ig-daniel-bodky),
[Heather](https://devopsdays.org/events/2024-amsterdam/program/ig-heather-thacker) and
[Marcel](https://devopsdays.org/events/2024-amsterdam/program/ig-marcel-britsch) are probably best summed up as

> A case against automation of baking while running against walls to end civilisation

You had to be there...

# My turn

When I found out that my talk would take place in the "Grote Zaal", I internally protested

> but but but, I thought you'd put me in a small room

But externally I said

> that's exciting!

![Grote Zaal](/images/devopsdays_amsterdam_2024_grote_zaal.jpg)

I thought my talk went well, hopefully some people don't think 
[legacy as a swear word]({{< ref "worst_jobs_for_best_engineers.md" >}}) anymore. It was really nice to have
support from a Dutch contingent of friends sat in the front where I could see them! I guess this is my way of
not so subtly suggesting if you know someone who might be nervous, give them courage by having Dutch friends there.
I'm sure that's what Dutch courage means, no?

![Legacy Cars](/images/devopsdays_amsterdam_2024_legacy_cars.jpg)

# Beers and the DevOps Bassoon

My memory of the evening are a little bit hazy, there may have been some alcoholic beverages involved, and I'm not
sure how the DevOps Bassoon was actually created but I'm sure Jeff and Gavin will know...

# The final day

The last day kicked off with [Sasha](https://devopsdays.org/events/2024-amsterdam/program/keynote-sasha-rosenbaum) talk
to us about failing to learn. A really interesting session on Growth mindset, fear and what we can actually do to 
help facilitate learning. Because you can't actually teach anyone, you can only help them learn! There were a lot of 
themes in the talk that very much reminded me of psychological safety and how important it is. But connecting it
to learning, and creating an environment where learning is easy makes it even more powerful!

Knowing too much, or overthinking, can actually inhibit learning:

![Legacy Cars](/images/devopsdays_amsterdam_2024_lucky_learners.jpg)

# Nerd sniping

The next talk I went to was amazing: [Katie](https://devopsdays.org/events/2024-amsterdam/program/katie-schilling) 
described how [fly.io](https://fly.io) had built a whole new way of developing software by using 
[nerd sniping](https://xkcd.com/356/).

![Nerd Sniping](/images/devopsdays_amsterdam_2024_nerd_sniping.jpg)

Actually, she lied and it's still just based on the [agile manifesto](https://agilemanifesto.org) and all about
giving people enough slack to work on interesting problems that provide value. Power to "People over Process"!

# More AI

[Yishai](https://devopsdays.org/events/2024-amsterdam/program/yishai-beeri) gave an interesting talk about GenAI and 
how to measure the impact. Having some way of tagging what code is generated and what is humanely written might be a
good start. I also liked his question on whether all the organisations that will be using AI to generate 10 times
as much code are ready for the sheer volume of code changes, processes, capacity of the pipeline and blockages
for code review!

# There and back again!

I really liked the story that [Tyko](https://devopsdays.org/events/2024-amsterdam/program/tyko-kieskamp) had to tell.
[Picnic](https://blog.picnic.nl) is an online supermarket, and they started in the cloud, then went hybrid and then
went back to being 100% cloud native. It was a fascinating tale of how latency requirements on cyber-physical 
systems (the scanners that route the boxes only have 250ms to make a decision whether to go left or right) meant a 
hybrid solution was initially less risky. What was really cool is that they figured out that while their initial design
was working, it also was a great example why hybrid isn't such a good idea, because there was a lot of duplicated
effort and extra cost in running their own hardware. So they went back to the cloud with a phased migration that was
almost derailed by a hand-held scanner. Apparently the scanner didn't like the extra HTTP redirect that they put in when
they change the backend system. Instead of a DNS change, they redirected to the new URL, and the scanner that they
were using to test, couldn't handle redirects. Or in other words... "it's always DNS"

I loved the story, it also drives home the point that it's ok for designs and systems to change, and to not get
the right solution on the first try.

# How to talk to a non-techy

The last talk of the conference was by [Amber](https://devopsdays.org/events/2024-amsterdam/program/amber-vanderburg)
who made us do work! We had to think of our own questions and figure out how to talk to non-techies. Being able
to clearly articulate problems or concepts is really important. I think I managed ok, when I explained CI/CD to a 
minivan in about three sentences... (this makes sense if you were there)

![Burn CD](/images/devopsdays_amsterdam_2024_burn_cd.jpg)

# Conclusion

I hope this gave a good insight on what DevOpsDays Amsterdam was all about, and I haven't even mentioned the
amazing open spaces, or some talks that I missed out on, great food, great people, great chats and some idiot
on telly blathering on about legacy:

![Idiot on Telly](/images/devopsdays_amsterdam_2024_idiot_on_telly.jpg)





