---
title: "Weakly Link 25/47"
date: 2025-11-23
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_47_title.jpg"
---

This week there have been some interesting bugs. Or interest in bugs. 

# Bugs

It was Cloudflare's turn to break the internet. As per usual, the 
[transparency on display](https://blog.cloudflare.com/18-november-2025-outage/) is rather cool.
It was rather interesting that for once, the problem wasn't DNS or BGP, neither was it
a cyberattack. Though indirectly, the threat of bots was responsible. As a subtle change
in the handling of queries meant that the bot management system suddenly produced config
files that were twice as large and that made them larger than the system could handle.

# Talking of subtlety

I stumbled across a great [thread on mastodon](https://mastodon.gamedev.place/@TomF/115589875974658415)
that illustrated how an interaction of the physics model in Half Life 2, a guard that stands too close
to a door and a change in compiler handling floating point units can mean that the game never
makes it past the famous intro sequence. Worse still, the bug travelled back in time and caused
the original version to fail to run. Well worth a read.

# Reliability is hard

A rather long read is this [article by authress.io](https://authress.io/knowledge-base/articles/2025/11/01/how-we-prevent-aws-downtime-impacts)
on how they try to mitigate downtime impacts. It's an interesting read, but I wanted to highlight
it for this graphic:

![sea of blue](/images/weakly_link_2025_47_sea_of_blue.png)

The amount of red inside the blue bar represents what 5 nines mean. 5 minutes and 15 seconds of downtime
in a year. Yes, there is a solitary red dot inside the black dot. I still don't understand how
some people can come in and demand 5 or 7 nines of reliability without really understanding what
it means and how hard it is. I think that graphic goes a long way in illustrating that basic math.

# Third party components

One of the interesting things about the authress piece is that they stress the importance of simplicity 
and reviewing third party components. Unfortunately, this works in two ways. 
[FFMPeg wants Google to stop sending bugs](https://thenewstack.io/ffmpeg-to-google-fund-us-or-stop-sending-bugs/).
Well, not quite, they want to stop sending AI slop bugs. Or actually come up with some funding rather
than just demanding fixes from volunteers. Open source is great, but the reliance of some companies
on open software without giving much back is a bit frustrating. And no wonder that some maintainers
burn out and then unwittingly hand over maintenance to scammers.

# Talking of Scammers

I thought this piece on [the future of gigawatt datacentres](https://pivot-to-ai.com/2025/11/07/the-futile-future-of-the-gigawatt-datacenter-by-nicholas-weaver/)
was quite thought-provoking. [Last week]({{< ref "weakly_link_2025_46.md" >}}) I was sceptical that the
benefit of the AI bubble was energy infrastructure. But Nicholas Weaver argues that the future of LLMs isn't
in huge energy hungry models, but ones that would be a little less precise but a lot cheaper. And if 
a few Mac minis can be used for inference, would it not be logical to say that having massive
white elephantine energy is not actually going to be that useful.

# Bubble Pop

Along the lines of bubbles, I thought [this piece](https://thoughtfuleng.substack.com/p/dear-cto-its-not-2015-anymore) 
was quite thought-provoking:

> Engineering feels (rightly) that they aren’t understood and appreciated. The rest of 
> the business feels (rightly) that they’re dealing with a withholding, black box.

Engineers and Engineering Managers used to just have to care about Engineering and rake in
obscene amounts of money (not that I'm complaining). It's no longer about
providing a nice place to work and protecting the ninja rockstars from such mundane things as
building value. Explaining what engineering does while understanding the business is going
to have to be skill for every engineer. And successful businesses will have to understand what
engineering does. Not just think of bums on seats...

# Bureaucrazy

When it comes to understanding business and untangling engineering, I thought the following piece
by Eidur was rather cool: [Botox](https://sonofalfred.substack.com/p/botox) talks about how creativity
is about being able to think of something new. And the opposite of creativity is bureaucracy.

> In an ideal world, we would choose the relative amount of creativity and bureaucracy as appropriate 
> for the situation, but this is very difficult due to The First Law of Humans:

> The bureaucrats always take over.

I do also like the following sentiment:

> It does not matter if the dogma is a currently-fashionable one or something now-discredited, dogma 
> always means that people have stopped thinking and creativity has died - it’s pure bureaucracy. 
> Agile is not better than Waterfall when they’re both in the form of dogma.

The solution? Stop thinking about technology in isolation. It's always about people and processes.

# Technology in Isolation

To finish off, let's look at an excellent piece on solving FizzBuzz. How complicated can you make it?
I did [something similar]({{< ref "fizzbuzz_without_ifs.md" >}}) a while back, but the mathematical
geekery in [FizzBuzz with cosines](https://susam.net/fizz-buzz-with-cosines.html) is pure joy!

That was the weakly link, goodbye.
