---
title: "What makes a good developer"
date: 2022-02-13T00:32:20Z
tags: ["agile","interviewing"]
featured_image: "/images/what_makes_a_good_developer_title.png"
---

A few days back as part of a general discussion about interviewing at [Equal Experts](https://www.equalexperts.com/our-people/our-network/), 
we looked at the question "What makes a good developer?"  Could we come up with a list of qualities in a developer 
that we'd want to look for?  This post illustrates my thinking.  

# Why do you ask?

To put it in a bit of context, I'm a software developer, I'm not a recruiter, but I've been involved with technical 
interviewing for quite a while, and have marked a fair number of take-home tests over the years, but recently we 
thought that the experience was not as good as it could be.  Neither for the candidates when occasionally nerves get in
the way of showing off their achievements, nor for the interviewers when people that were lacking skills were being
put forward.  

To answer the question, should we offer this person a contract, my gut feeling had always been to check "Would I want them on my 
team? Would I want to work with them?" However, that is very subjective and it doesn't really scale.  When lots
of different developers carry out technical interviews, it would be good if we were all singing from the same hymn 
sheet.

So, let's dig a little deeper

# What makes a good developer?

In my opinion, the following criteria are crucial:

1) values collaboration
2) can communicate ideas
3) understands and practices agile
4) understands their language of choice and is open to learn others
5) builds automated tests
6) knows how to build systems securely and at scale

I intentionally kept the list short, otherwise it gets very difficult to use the criteria.  If I had 13 bullet points
on what makes a good developer, I don't think I would be able to evaluate anyone.  I'd have forgotten point 2 by the 
time I reach number 10.  I also kept the criteria quite short (some might say vague) to allow for a bit of room
when interpreting them.  I'll try to give a bit more context below:

# 1) Values collaboration

Software engineering is a social thing.  It is practically impossible for any one person to carry out software 
development projects on their own.  This image of the lone wolf developer that locks themselves in a room and a few
months later comes out with the killer app is just fantasy.  In professional software development we don't work 
on our own, we work in teams.  And I would argue (and I'm not alone on this) that development works best in groups
of people:

* That's why we like to gather around a whiteboard (physically or virtually) to work out tricky problems.  
* That's why we talk to our colleagues about the problem we're having with a particular system
* That's why we review each other's code
* That's why we practice pair programming
* That's why we practice mob programming

How often have you tried to solve an issue on your own, banged your head against a particular problem and solved it within
a couple of minutes by trying to explain it to a fellow developer? How often does a colleague spot an obvious error 
that passed you by?  That's collaboration. 

I'm not a fan of hard and fast rules, but there's a couple of them that I try to practice wherever I go:

* If you have a question, don't try to be a hero and spend half a day trying to solve it yourself, when shouting it out 
  across the room (or team Slack channel) could mean someone answers it straightaway.
* If someone asks you a question, try to help immediately

Collaboration means that knowledge is actively shared and not hoarded and helps to stop potential blockers from becoming 
actual blockers.

Collaboration isn't something that just happens between developers either.  A good service developer will know how to 
collaborate with other service teams, with an infrastructure engineer, get the best information out of testers and user 
researchers, knows how to talk to end users or business stakeholders.  This does not mean they have to be specialists
in all those areas but need to know how to talk to them, listen and understand.  In fact, I have no time for developers
that look down on QAs or user researchers, thinking they know better or thinking they are better.

I put collaboration on the top of my list, because I do think it's the most important skill for a software developer.

# 2) Can communicate ideas

Closely linked to collaboration is the ability to communicate ideas.  Software engineering is about creating lots of units
of code that process input and create outputs.  These systems cannot be designed, written, tested in isolation.  Some
of the most interesting incidents happen when a system does not behave how we expected it to.  So I find it hugely
important that developers are able to communicate their ideas.  Based on my experience, the "cutting of the code" bit
of software engineering is (relatively speaking) the easy bit.  Coming up with the patterns and templates or deciding
which ones to use is the hard bit.  

Too often have I heard "this wasn't in the requirements" when something failed.  No, as developers we cannot just rely on
someone else to tell us what we have to write.  That is not what coding is about.  Requirements need to be challenged. 
If something doesn't add any value to a system, why spend time building it?  If something is just gold-plating, there
needs to be a valid reason to expend energy to build it.  In order to be able to do that, a developer has to be able to 
communicate how a system works.  Often this communication has to cut across different levels of technical expertise.

More often than not, this involves writing diagrams and talking around it.

During interviews, I am a fan of this question: "tell me about a system that you've recently worked on and draw it
to me".  Drawing should just be simplistic lines and boxes.  If I can have a conversation about such a system with the 
candidate after a few minutes and I am able to ask detailed technical questions, then I know something is going right.  
If I detect some passion (I loved working with this because X, or that bit was utter shite because Y), that's what 
I would be looking for.  By that I don't mean that people need to get giddy about implementing every RESTful TPS service, 
but as engineers I think it's not wrong to care about the things we build.  By the way, I think looking after legacy
systems can be just as exciting as starting with a blank canvas of a greenfield project. 

# 3) Understands and practices agile

"Agile" is a bit controversial these days.  Personally, I think too many organisations are falling foul of agile theatre:

* Sprints with all the ceremonies (planning session, retrospective) but mainly moving stories that are way too big from  
  one sprint to the next.  
* 17 different states in Jira: Backlog, Ready, Selected, In Analysis, Ready for Development, In Progress,
  Waiting for PR review, Ready for QA, QA, Ready for Merging, etc
* Stand-ups involving repetitive chanting of "I worked PLP-123 yesterday, continuing today, making good progress, no blockers"
* Certified Scrum Masters
* Burn down charts

As soon as I see any of the above, I think it becomes clear that the people involved do not really understand what the
[agile manifest](https://agilemanifesto.org) was about.

> We value people over processes

* Doesn't really lend itself to having 17 sub states for epics in Jira.
* Doesn't really lend itself to agonising in 6 hour meeting day whether sub-ticket PLP-124 should be 5 or 8 story points

I understand that there is often an organisational desire to measure and standardise developers.  I also get that some 
~~people~~ beancounters don't understand that developers are not "resources", and cannot just be swapped out by bringing in 
cheaper contractors or graduates fresh out of university.  But I don't understand when developers themselves don't
look to review their ways of working, and if something doesn't work, it doesn't get dumped because that's what we've
always done; or that's what the certification says we should do.

## Ok clever clogs, so what does agile mean to you?

To me, agile is about realising that a software is developed by people.  And the best way to increase productivity
is to remove blockers.  This can only be done by regularly reviewing your ways of working and asking yourself whether
it is worth changing.  Learn from failure, think about "what good looks like", and try to get there

![XKCD: Good code](https://imgs.xkcd.com/comics/good_code.png)

Personally, at the moment I think "what good looks like" is as follows:

- Simple kanban with three columns: Todo, Doing, Done.
- Simple estimates: [1, TFB, NFC](https://estimation.lunarlogic.io)
- Automated tests 
- Continuous integration/deployments 
- Test Driven Development 
- Small teams 
- Pair/mob programming (though I think sometimes going off on your own can be beneficial too)
- Frequent retros (though don't have to be at fixed intervals)
- Postmortems (learn from failure)
- Fast Feedback Loops
- Celebrate success

# 4) Understands their language and is open to learning others

> Wait, so we're talking about interviewing developers and only now do we talk about the programming language?  I thought
> this was a Java job...

This is what job hunting usually starts out with isn't it?  A recruiter will ask for a CV which typically contains
an ever-growing list of languages and frameworks and a number of years of experience.

But scratch the surface and that list is pretty useless.  What does it tell me if someone has 10 years of 
experience with Java, Oracle, XML or JUnit.  Nothing.  I've marked tests by people with 10 years experience that
were making the most elementary mistakes, and I've interviewed people that were 2 years out of university that 
taught me new things during an interview.

When I am interviewing and thinking, would I want to work with this person on my current project, I 
do have to think about the project.  I am a contractor, and I don't particularly want another contractor joining 
the project that has no experience with the main language.  If I'm on a Scala project and the candidate only 
knows C#, then that will mean that they can't really hit the ground running.  But should this mean that when looking
to fill a position, we should only be looking for people that specifically have the exact experience that everyone
else on the team has?

No. For a couple of reasons:

1) It'll never be possible to find someone that has the exact combination of language, platform, framework, test 
   framework, infrastructure, cloud platform that the project is on.  Let alone be familiar with the codebase, so
   there's always going to be a period of adjustment.  
2) It is very, very, very useful to have a team with a diverse set of skills.  While something might not be a 
   data engineering project, it sure is useful to have someone with a data background.  Or DBA, or frontend, or 
   middleware... Diversity is very useful!

## What about "is open to learning others"?

I find it off-putting when people show no interest in learning new programming languages.  I think learning Scala
made me a better Java programmer, and trying to learn Haskell made me a better Scala developer.  Now this does not, 
and I cannot stress this enough, mean that I think Haskell is better than Java, far from it.  Different programming
languages allow for different perspectives, and ideas and concepts move between languages.  The Java of today looks a 
lot different to the Java of 10 years ago.  If Java developers hadn't looked over to how Scala or C# did things, would
Java have stayed still?

I think it is important to be open to change and to keep learning.  For me, a candidate would ideally demonstrate that 
they are comfortable with multiple languages and open to learning new concepts,
especially as I've seen a lot of developers and projects appear to be content in stuck in the past.  "I'm not
comfortable with streams" is not a great advertisement for a developer when streams were introduced in Java 8 and that
was released in 2014.

# 5) Builds automated tests

Building automated tests is essential:  

* Without tests that can be run at a touch of a button, it is difficult to refactor with confidence.  
* Without automated testing, we cannot deploy frequently

... I could go on, but I'll just point you to Dave Farley's [YouTube channel](https://www.youtube.com/channel/UCCfqyGl3nq_V0bo64CjZh8g)
as he's far better at extolling the virtues of Test Driven Development and Continuous Integration.  I do think 
that attitudes of "I don't have time for testing" or "my code doesn't have bugs, why would I need tests" have got 
absolutely no place in software engineering.  It's been shown time and again that testing increases quality AND
speed.

So I think a candidate would have to be able to demonstrate that they're comfortable with testing and it's something
that is second nature.  And a simple take-home test with unit tests as well as a short pairing session with TDD
can easily draw that out.

# 6) Knows how to build systems securely and at scale

My last criteria boils down to experience in working with complex systems:

* It is one thing having a simple service with a few 1000 requests an hour.  But scale such a system up to 1000s of 
  requests a second needs different considerations.  How can you observe the behaviour of the system, would you really
  want to have hundreds of log messages per request?  Can the database handle the load?  What happens when
  this query is run against a couple of million records?
* What would be security considerations?  If a user can upload files or XML, could that be exploited?

Again, I don't think developers need to have specialists security knowledge, but I would be disappointed if a web 
application developer wasn't aware of the [OWASP top ten](https://owasp.org/www-project-top-ten/) or a Java developer
does not have an understanding of the garbage collector.

# Putting it all together

Finding good people to work with can be very tricky.  All to often, interviews don't tell the whole story - people can
be nervous or just have a bad day.  Take-home tests can be frustrating to mark.  Developers have a lot of transferrable
skills (just because they don't know some technology at the start of an engagement, doesn't mean they wouldn't be able
to pick it up quickly).

I hope the above criteria helps finding good developers.  As always, a software engineer's favourite answer to any question
is "it depends...", so there isn't really room for absolutes in defining what makes a good developer.  Length of time
in the industry can be a basic guide, but hearing war stories is probably a better indicator.  Simple take-home
tests can be quite revealing on how much care and attention people put into their code and/or whether they overcomplicate
solutions (please don't do [this](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition)).  Pairing
exercises are really useful to see how well people talk about their code (but shouldn't be used to write mammoth
tasks).

# What I don't think works

Take-home tests and pairing exercises should not be onerous.  When developers look to change jobs, they shouldn't have
to spend a weeks worth of work, just to fail at the 5th interview.  I don't think that is fair on developers.  

I also don't think that outsourcing these tests to places like leetcode is ideal.  I don't think leetcode lends itself
to assessing whether people write maintainable code.  Much better to judge it yourself and check whether this is code that I would want
to pick up 6 months after the developer moved onto a different project.  I also don't think that hand-implementing sorting 
algorithms in less than an hour is any indication as to whether someone will fit well into the team.

# What is really needed

Software engineering is a collaborative effort, as such the ability to collaborate, work well with others, a diversity
of ideas and skills really makes a team fly.  So that's exactly who I'm looking for during interviews. 
