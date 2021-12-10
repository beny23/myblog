---
title: "Context and Lines - My Take on Engineering Room Ep 1"
date: 2021-12-10T16:30:32Z
category: ["my-take-on"]
---

[Dave Farley](https://twitter.com/davefarley77) and [Martin Fowler](https://twitter.com/martinfowler) are two heavyweights
when it comes to Software Development.  As well as his latest and hugely anticipated book 
[Modern Software Engineering](https://www.amazon.co.uk/Modern-Software-Engineering-Really-Better/dp/0137314914),
Dave wrote [Continuous Delivery](https://www.amazon.co.uk/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912).
Martin co-wrote the [Agile Manifesto](https://agilemanifesto.org) and [Refactoring](https://www.amazon.co.uk/Refactoring-Improving-Existing-Addison-Wesley-Technology/dp/0134757599).
All of which are hugely influential to large swathes of software engineers.  So when both came together for Dave's new series on YouTube, the 
[Engineering Room](https://www.youtube.com/watch?v=0TwoubGSXpc) (sponsored by [Equal Experts](https://www.equalexperts.com/)), it was bound to be very interesting.  

This blog post provides my personal key takeaways from watching the chat.  I've been working in the software engineering
profession for nearly 30 years, so I've seen a thing or two.  The principles of the agile manifesto and continuous
delivery have been hugely influential to my preferred ways of working and I am always eager to learn more. 

Initially, Dave asked Martin about what he was working on, which drew up an interesting observation.  Martin had not
been at the coalface of software development in a long while and is now also taking [a bit of a break](https://martinfowler.com/articles/202106-reducing-speaking.html),
instead focussing on mentoring and helping his colleagues. Something he's done for years now.  In fact, he said his
greatest strength isn't coming up with new things, but rather explaining concepts.

# Distributed Systems

First Martin talked about his work with Unmesh Joshi on [patterns of distributed systems](https://martinfowler.com/articles/patterns-of-distributed-systems/)
which put a spotlight on distributed systems.  While it is not necessary to design your own systems, it is very much
essential to understand the workings of the components of distributed systems (such as messaging systems, data stores
and distributed consensus protocols) in order to utilise them effectively.  In modern software engineering, more and more
work is being done in a distributed nature, and that distribution leads to an "explosion of complexity".  In fact, Martin
and Dave both agree that "avoiding distribution" for as long as possible is a good thing, as distribution always acts as
a "complexity booster".

So the work at ThoughtWorks involves trying to distill patterns of distribution, trying to understand how to use such
systems effectively.  Unmesh is building simple implementations of such systems, then comparing them with the real
open source project implementations to derive examples, which should make for fascinating when it comes out.  
Good examples, incidentally, are very hard to create: they can't be so realistic to make them difficult to understand, 
but have to be realistic enough to allow you to understand what's being explained.

What makes distribution hard is the interaction between systems, Martin phrased it really well.  Slightly paraphrasing,
"looking at a diagram, it's not the blobs that are difficult, it's the lines".  I couldn't agree more.  So often do you
hear the clichee "but it works on my machine", where even the simplest service can be tripped up by a dodgy network
connection, or an API call that has subtly misrepresented the output.

Another important takeaway on the discussion of distributed systems was that it's context that's important.  The patterns
that Martin and Unmesh are trying to draw out always depend on context.  There is no absolute truth in developing software,
some patterns work in some situations, while others do not.  Monoliths aren't wrong per se. It all depends on the context.

# Data Mesh

Then the conversation turned to another book that Martin is involved with.  Zhamak Dehghani is working on  
[Data Mesh](https://martinfowler.com/articles/data-mesh-principles.html). I found this really eye-opening. 
The traditional approach to analytical data is:

- get lots of data sources
- centralise the data
- provide central tools

The fundamental problem with this is that it just does not scale. In a sufficiently big organisation, it is impossible
for a centralised hub to provide the right tools and models to be able to rapidly make use of today's tidal wave of
analytical data.  Instead, the focus should be on providing a platform to allow the domain owners to provide customised
analytical feeds as products.  This then allows the experts on the data to provide the product and those products
to be combined in innovative ways.  If all that relies on a central team, this will inevitably lead to bottlenecks.
As a way of driving home the point, Martin mentioned that for some organisations it will just be too difficult for a single
person/team to keep a model of the whole system in their head.

I think of this as analogous to the decentralisation afforded to the operational side of software by the way of breaking
monoliths up into microservices.  When the model is small enough, there's nothing wrong with a monolith, but when it gets
too big, that centralisation will act as a limit on the achievable speed of development.  Similarly, when the model is
small enough, the centralisation of waterfall can work, but in a sufficiently large and dynamic system, when agile is
done right nothing beats a decentralised approach.

Martin also mentioned something interesting, the size of the challenge on the analytical side often tends to be much larger
than on the operational side because instead of dealing with just the current state, the historical information needs
to be taken into account when looking at scale.  I found this very insightful and am eagerly awaiting the 
findings on patterns for data mesh.  

As before, the main challenges are contexts and lines.  Business units publishing their own analytics turns an oversized
data lake into multiple streams and ponds.

# Legacy Displacement

Next, Dave and Martin talked about the big elephant in the room.  So much work in software engineering is being
done on replacing legacy systems.  But there's not that much written about it.  Martin is working with Ian Cartwright,
Rob Horn and James Lewis on [patterns of legacy displacement](https://martinfowler.com/articles/patterns-legacy-displacement/).
How do you replace that old mainframe.  And no, a big bang rewrite of everything almost never works.  Dave attested to that
and I can remember lots of projects that would "fix all of our current problems when it's finished in 15 months time".

One of the most striking remarks made by Martin was "tomorrow's legacy systems are built today".  This has several
interesting implications:

- it doesn't mean that today's code is rubbish
- it also doesn't mean that old code written is rubbish
- usually there were good reasons to write a legacy system the way it was

The key is that software is a gradual and constant process.  The context for building something yesterday may
have changed today.  In my opinion, this is why the basic principles of the agile manifesto remain so important today.
Martin called it compassionate coding, and he stresses the importance of trying to understand the context of when a system 
was written.

But back to transforming legacy systems.  And the book Martin is helping with.  And patterns.  The first pattern
Martin mentioned is an anti-pattern: Feature parity.  That's when people just re-engineer the same system using today's
technology without asking about whether the same context still applies.  The "Go for feature parity and put it into the
cloud" legacy migration is almost always a recipe for failure.  Personally, I think of it this way:  If you use the
same features as a legacy system, then you're creating a legacy system.  The benefits of cloud are not virtualised
tin. Just spinning up a lot of EC2 instances and installing the old software is only very rarely a good idea.  Of course
Martin cautioned on absolutism here, don't say "never a good idea", because it depends on the context. 

Another interesting example during Dave and Martin's chat was the idea of "divert the flow".  Rather than taking the
conservative approach of starting a transformation at non-critical systems and working inwards, it is often better
(if a little counter-intuitive) to rip out what's termed as a "critical aggregator" (a term for a system that
is critical for a business and used to make decisions).  This component is usually wired into so many other components that
replacing it can be difficult.  But on the flip side, it is difficult to replace anything else while it still exists.
I think there is a very valid point there.  Solving the hard question first can lead to much better success than going
after low-hanging fruit and then finding a redesign is necessary anyway.  Incidentally, the pattern is called "divert
the flow" because to fix a dam you have to divert the river first. Nice metaphor!

Personally, the pattern I keep seeing in this conversation is that the tricky things are lines and that it depends on context.

# Patterns for Success

Next in their chat, Dave asked Martin his opinion about patterns that are likely to lead to success.  (We shall not
forget the mantra that nothing is absolute). While techniques change, there seem to be a few fundamental patterns:

- self-testing code - it is important to be able to push a button and get quick feedback on whether you've broken anything
  (TDD is a very successful technique for doing this)
- the humble object - use small objects that can be independently tested
- small cycles and fast feedback

Like so much it is context, context, context that tends to drive what's successful and not, and a good example of this is
"feature branching".  Feature branching emerged from the Open Source Software world where a lot of contributions are
sporadic and not necessary by people known to you.  In that context it makes sense.  When it comes to working in a pizza-sized
team of trusted colleagues, the problem with feature branches is that the "integration frequency" is constrained.  
Integration and feedback can only happen when a feature is complete, and it does not tend to be possible to make
features small enough to be able to integrate often enough.  That then has an impact on the ability to refactor.

Martin brought up this [quote](https://twitter.com/skamille/status/1198350623747584002?s=20) -

> Conflating OSS and private software development team needs is like the original sin of current software development rituals

and I couldn't agree more!  

Martin ended with an interesting observation: one issue with software is that its output is not easily measured (though
[DORA](https://nicolefv.com/dora) offers some hope) and the science of success is more sociology and not physics.
Software is cheap to create and we can change it easily.

# Conclusion

I really enjoyed listening to Dave and Martin, and I was struck by how the importance of context and the lines between
components is a theme across the variety of topics covered in the chat.  It is always important to understand why
decisions were made, not just how they were implemented.  Software engineering is richer and better for having 
experienced practitioners who understand that the success of software is tied to business outcomes and listening to
their advice.

Also published on the Equal Experts [blog](https://www.equalexperts.com/blog/our-thinking/key-takeaways-episode-1-dave-farleys-the-engineering-room/) 