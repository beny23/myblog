---
title: "What do you think is the biggest challenge faced by the software product engineering community today?"
date: 2022-09-08T03:30:32Z
tags: ["conferences", "agile", "agile-india", "agile-india-interview"]
featured_image: "/images/agile_india_2022_speaker_interview_questions_2_title.png"
---

This is part of my [series of interview questions](/tags/agile-india-interview/) for [Agile India 2022](https://2022.agileindia.org).

It is difficult to articulate what the biggest challenge to software product engineering is, because there's a fair 
few of them, so I'll try to describe a few of my bug bears.

# Process over people

In my opinion, a huge issue that we unfortunately find all too often is when the principles of the 
[agile manifesto](https://agilemanifesto.org) are ignored or not applied. My gut feeling is that there are a lot
of developers out there who have never even heard of it.  [It took me years]({{< ref "agile_india_2022_speaker_interview_questions.md" >}}) 
of being in industry before coming across it.

I find it very disheartening to find structures where control is applied from above rather than giving teams the
agency to make decisions for themselves.  This does not mean that there should be anarchy and every team for
themselves, but productive organisations have realised that the best way of getting the most out of people is to
make them decide how to do something.  They tend to be the best at figuring out what works for them.

My best anecdote around that when I was working in the financial sector and an organisation got hit by a fine because
a change made it into production that negatively affected their customers. The knee-jerk reaction was to ensure that
every change had to link back to a Jira ticket and tests and nobody should be able to deploy without getting
sign-off from a release management team.  That lead to infighting amongst teams who should be allowed to deploy
to the test and production environment and all in all to a rather toxic environment.

I think it is extremely unfortunate that agile and collaboration is not taught more in schools and university, who
sometimes forget that working together in software isn't cheating.

# Bums on seats

The next best thing for turning software engineers into a pitchfork-wielding mob (in my opinion anyway) is to call them
resources. It's pretty disrespectful for anyone to be called a resource. There is a huge variety of skills and 
experiences that engineers bring to the table.  First and foremost the fact that technologies are changing and a
good engineer never stops learning. So how can that be stuffed into a pigeonhole marked "senior developer"?

![Contractor Conveyor Belt](/images/agile_india_2022_speaker_interview_questions_2_contractor_conveyor_belt.png)

I'm fascinated by the regularity that organisations bring in contractors to help with a project, for those same
contractors to build up a lot of domain knowledge and expertise only for someone in accounting to say
"wouldn't it be a lot cheaper if we replaced all these people with..."

- ... graduates
- ... permanent staff
- ... offshore agencies
- ... big consultancies (who then send graduates)

Now, don't get me wrong, I am not advocating for engineers that have to stay in the same place all their career or
contractors that can never be fired, that would be ridiculous, but I think organisations need to recognise that
the "brain capital" that they've got cannot be treated like a simple replaceable commodity. It takes time and effort
to do handovers properly, to transfer knowledge.

I've worked on plenty of systems that are legacy code where people are too afraid to make changes because the
teams who know how things worked have long since gone and the only thing that was handed over was the source code.

So don't treat developers like resources, recognise the skill and effort they put in and do proper handovers!

# No-code, Low-code, AI-code

Now, this is an interesting challenge and I don't think is as much of a threat than a distraction that will keep software
engineers in a job for a long time.  The concept that we can just click a few buttons and easily create complicated
applications comes round with predictable regularity.

- Who remembers all those MS Access databases and forms that were great on a single computer but ground to a halt once
  more than one person wanted to use it.
- Who remembers all the Visual Basic applications that created lots of legacy code that then had to be completely
  rewritten when it no longer worked properly
- Who remembers all those Dreamweaver websites that create HTML pages that nobody could use or maintain

Again, I have to qualify this, I don't think there is anything wrong with low-code builders or GitHub's co-pilot, but
we have to recognise it for what it is: a shortcut to typing in code.  And what you sacrifice is usually the difference
between "typing in codez" and engineering software.  Most experienced software engineers will tell you that typing in
the programming language is not the thing that occupies most of our day. It's the design and thinking about how
your systems will fit with what the user needs. That's often very different from what a user wants.

In the no-code, low-code world we'll have:

- people hacking together something because it's cheap and then experienced engineers having to fix it because it does
  not work properly
- hiring expensive no-code-whisperers to make the platform do exactly what it should, i.e. expensive engineers

With AI-based coding, we can add in the complicating that the models that pick the pattern to match are very opaque
so how can we be sure whether bits of code are copied from something efficient or based on some unintended pattern.

```
IF unanticipated_edge_case() AND
   unintended_stereotype_in_training_data() MATCHED
THEN
  ruin_an_individuals_life();
END  
```

# Final thoughts

I think in software engineering it is easy to forget that at its heart it is a social and collaborative process.
Software is not created by hoodie-wearing loners in a garage, it is created by teams of people who collaborate
to get the most out of their creative thinking and engineer a solution.  And as such anything that prevents that
such as treating of people as resources or replacing them with robots might look good on some short-term financial
reports but is not going to be rather expensive in the long run.



