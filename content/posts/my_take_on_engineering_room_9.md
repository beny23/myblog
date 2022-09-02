---
title: "Less is more agile"
date: 2022-09-01T03:30:32Z
tags: ["my-take-on", "agile"]
featured_image: "/images/my_take_on_engineering_room_9_title.jpg"
---

[Dave Farley](https://twitter.com/davefarley77) and [Allen Holub](https://twitter.com/allenholub) are two people that
I respect hugely when it comes to Software Development. I've been following them on twitter for quite a while, and
am always taken in about their takes on driving continuous delivery and "lowercase agile" forward.  So when both came 
together for Dave's [Engineering Room](https://youtu.be/hxXmTnb3mFU) (sponsored by [Equal Experts](https://www.equalexperts.com/),
who I work with), it was bound to be very interesting.  

In this post I try to outline my key takeaways from watching the chat. It might also just be a rant.  I've been working in the software engineering
profession for about 30 years, so I've seen a thing or two. The principles of the [agile manifesto](https://agilemanifesto.org) 
and continuous delivery have been hugely influential to my preferred ways of working, so it was interesting to see how my 
observations compared.

# What agile is not

The chat started off with some great quotes:

[Andy Hunt](https://twitter.com/PragmaticAndy):

> Agile has come to mean doing half of scrum poorly and using Jira

[Martin Fowler](https://twitter.com/martinfowler)

> SAFe is Shitty Agile For Enterprises

Allen himself referred to one of [his tweets](https://twitter.com/allenholub/status/1514806761873575936): 

> there's not much he doesn't like about Scrum, just Backlogs, Sprints, Daily Scrums, Sprint Reviews, POs

The problem is that Scrum started out as a lightweight wrapper around [Extreme Programming (XP)](http://www.extremeprogramming.org)
to make it palatable to management, and it is all that extra cruft that people of added that is the problem.  And 
unfortunately Scrum means focussing on those extra parts that make it palatable to management.  And that's just not agile.

Allen particularly disliked the recent notion of adding accountability to agile.  "I am going to hold you accountable" sounds
like a threat rather than "we value people over processes".

The problem is that corporate clients are where the money is.  And as soon as money gets involved, things get complicated.
Scrum trainers have an interest in teaching frameworks and selling courses.  There's no business case to be made for
an agile coach to say: "Go and read the [agile manifesto](https://agilemanifesto.org), it's a quick read and easy to understand".

According to Dave, true agile has been one of the most significant steps in delivering software.  Compared to 
waterfall, it is fundamentally less constrained.  Waterfall demands that we know the system and all the possible problems
upfront.  When doing agile, teams work out how to best tackle a problem and deliver value quickly with fast feedback.
But because an agile practitioner can't answer "when will it be done" right at the beginning, this makes it a difficult
sell for "the business".  

# Don't estimate

At this stage, Allen mentioned one his bugbears: Estimation. In many projects I've been on myself, a manager will whip out a Gantt chart and 
demand to know when will it be finished?  What are the deadlines?  Are we there yet? Why are we only at 46.43% progress,
we are 17 days behind schedule!

Estimating is very hard. I've been in many Sprint Planning meetings, played planning poker,
and had hours worth of discussion whether a story has a Large or actually Small.  And at the end of it, the estimate
has to be thrown out anyway because the devs who picked up the story didn't have the same experience as the devs estimating.

The solution is simple: don't estimate!  Don't spend the time trying to work out how long something will take!  If you 
really have to, I quite like [no bullshit estimation](https://estimation.lunarlogic.io) - basically there's only 
three types of story:

- 1
- TFB (Too Fucking Big)
- NFC (No Fucking Clue)

Any more is wasted effort. Cut it out. I've never met anyone who enjoys those long, drawn-out planning sessions, where the 
whole team sits around a table and spends half an hour discussing a ticket that wouldn't take much longer to code.

Worrying about deadlines is dysfunctional.  Allen made this point about agile: 

> If you're not happy, you're not doing it right

That struck a chord with me.  One of my favourite principles from the agile manifesto is "people over process" - this
means people need to be able to make decisions on how they work.  As the people who do the work, will best know how to do it.

Allen illustrated this with a story where the Toyota process (lean principles) was used to turn around a GM car manufacturing plant in the US. 
Within half a year it went from a worst performer to a best performer.  Then General Motors had everyone document their
processes and dumped it to a different plant, where (no surprise there) it failed spectacularly.  A process that works in
one context can't just be transplated.  The same applies for Scrum.  Allen noted that he thought that when Ken Schwaber
came up with Scrum 20 years ago it probably worked brilliantly in that setting.  But it is not something that can just
be blindly replicated and just assumed as gospel.  

# No sprints

Talking of gospel (Dave quoting Allen):

> Agile has become a priesthood where the priests don't understand the rituals

I think this is a very apt observation.  I've had my share of agile coaches teaching developers about 
sprints and planning sessions, show and tells, backlog prioritisations and retrospectives and standup meetings.  It is ironic
that these are often referred to as ceremonies and the [dictionary definition of ceremony](https://www.merriam-webster.com/dictionary/ceremony) 
is:

> an action performed only formally with no deep significance

Now, I'm not saying that we should do away with all planning or retros or standups, far from it, but these things have
to be worked out by the team and decided what works for them, in their context. Note, I am also not saying that agile
coaches are useless, I've worked with plenty of clever people that really know agile, but there have been a few that
ended up beating any joy out of software development with the scrum guide.

Allen mentioned another problem with the ceremonial approach:  Don't wait for the next scheduled retrospective, if
something goes wrong, talk about it now.  Not in two weeks time when nobody remembers the details anymore.

# Fight Faux Agile

A very important point of agile is to actively question whether something is working.  It is not easy to stand
up and say "No! This isn't working" - but often it is only way to fix something.  Agile only works when it is
supported by an agile organisation.  I think [Conway's Law](https://en.wikipedia.org/wiki/Conway%27s_law) applies:
A rigid organisation will struggle to be agile.

# Use Agile to Get Agile

Allen described his work as a consultant when he goes into a new organisation:

* First he observes
* Then he would try to identify the biggest problem
* Then he would try change it (i.e. run an experiment)
* Then if that works, run another experiment
* If it doesn't work, go back

Important point - you can't document that and then put it into another organisation. It needs to happen organically 
and with context (i.e. just blindly adopting Scrum won't work).

It is also important not to take "lean principles" to literally.  In manufactoring you can't go back, but in
software engineering it is quite easy to undo.

# The Risk of Metrics

At this point Dave and Allen disagreed somewhat when they were talking about metrics.  Dave can see much value
in being able to measure to drive improvements.  Allen is a little bit more cautious. In his experience, metrics
are easy to abuse.  For example, if these numbers become managment metrics it can foster the wrong thing, i.e. engineers 
pushing out poor code quicker just to improve the "deployment frequency" metric.  According to Allen, the best way of looking
at metrics is as an indicator, not a hard and fast number.  Ultimately, the aim should be to make the customer's
lives better - that is not something that can be measured by a number.  How would you know whether you're doing the right
thing? Talk to people.

# Software Engineering needs Smart People

Software Engineering is hard and needs smart people to be successful.  Interestingly, very often it must be noted that
the hiring and HR practices are broken.  Recruiting people does not work by doing laundry lists of questions asked
by HR drones that don't understand context or demand 10 years of experience in a technology that's 8 years old.  Five
rounds of interviews, tests and exams where people have to recite algorithms by heart are pretty pointless.

![Interview vs Job Meme](/images/my_take_on_engineering_room_9_interview_vs_job.jpg)

Allen and Dave discussed that HR abandons trust too often.  There appears to be a myth around that when 
people's back is turned that suddenly everyone turns into a slacker.  In reality, people generally won't abuse trust.
And underperformance is often a problem of training and mentorship (or lack thereof) and absolutely does not get
fixed by threatening to fire people or putting them under extra scrutiny.

# Why can't hiring be agile?

This got me thinking, isn't doing things upfront the antithesis of agile?  But how else could you describe a working
relationship if a candidate has to prove that they can deal with every tech stack, framework and algorithm before they
even start a position?  When interviewing, technical skills should not matter so much, typically people will pick those up
quickly. Allen goes as far as saying that the drive of getting children into STEM to the detriment of all other subjects
will lead to worse software engineers.  It is not the technology that matters, it's interacting with people. 
The better engineers can communicate, the better a team will perform. 

To that end, teams should do their own hiring and work out "can I work with this person",
not look for "rock stars" or "ninjas".  Incidentally, Dave has a similar take in one of his videos on 
[software rockstars](https://youtu.be/mVY2rFninp8).  I keep thinking wouldn't it be nicer for everyone involved if 
hiring became more agile and "tested in production"?

# Final Thoughts

Software Engineers have a habit of forgetting the simplicity of agile.  All too often are solutions sought in process
and technology, when the first thought should echoing the manifesto's "people over process". That means talking to
people - be that users of the software, stakeholders, team members or colleagues from different teams. The simple 
solutions are often the most efficient, and that extends to not thinking "We can write a software tool for that" when
a few Post-It notes are sufficient.

So using that hook of "simplicity", the best advice for "doing agile" is:

1. Read the [manifesto](https://agilemanifesto.org)
2. Figure out what makes you happy. And do that. 