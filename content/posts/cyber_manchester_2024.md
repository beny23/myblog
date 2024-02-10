---
title: "Cyber Measures Up in Manchester"
date: 2024-02-09
tags: ["appsec","security","conference"]
featured_image: "/images/cyber_manchester_2024_title.png"
---

Peter Drucker said "What gets measured, gets managed". When I turned up at Old Trafford, home of Manchester's red team
(it's a security conference, geddit) for [The Future of Cyber](https://cybermanchester.events), I certainly was 
measurably impressed by the setting even though I'm usually found more on the [blue](https://www.evertonfc.com) 
spectrum of infosec.

![Manchester Red Teamer](/images/cyber_manchester_2024_red_teamer.jpg)

But let's get into the talks!

# Measure, measure, measure

First, Greg Notch talked about the importance of using metrics in communication. A culture of metrics allows us to measure
cost and value. I liked the following:

> Don't solve a $5 problem with a $10 solution

It is important to speak a simple language and use the metrics that can then allow us to spot trends.

![Choose Metrics Wisely](/images/cyber_manchester_2024_choose_metrics_wisely.jpg)

Couldn't agree more! Measurements also played an important part in Tim Ward's talk about nudging people to do the right
thing rather than scaring them. The basic premise was this:

> What's going to be more effective? Shouting at children to tidy or turning it into a game?

Well, I know that shouting falls on deaf ears for my children. Similarly, I really liked the idea of using nudges
instead of painful yearly "secure coding" exercises or sending people on training as punishment for clicking on a 
phishing simulation email.

I loved how time introduced the concept of choice architecture to made us think about how we can use behavioural theory
for better communication:

![Nudging to prevent phishing emails](/images/cyber_manchester_2024_nudge_emails.jpg)

The role of measurement was to ensure that the nudges and prompts make a difference. The biggest risks of prompts is 
habituation. How many of us get change-blind when faced with the same warning over and over again, regardless of how
red and flashing it is. Good prompts should warn us when we're about to do something that we haven't done in a while.

I really liked this approach and had a good chat with Tim in the vendor area. I got the impression that we both had
been scarred by mandatory training videos that cannot be sped up. Who thinks this is a good idea? But like he said in 
his talk: "People don't always make rational decisions"

# Let's Play Football!

Lee Morton then gave us a great demonstration on how to make difficult subjects more accessible. Now, I don't know
the first thing about American Football, but using the medium of infosec analogies, Lee was able to explain
"blitzing", "intercepts" and "pick-6s" in a language that I understand. I think his point was to do the reverse
for members of the board, who are not interested in jargon, but easy to understand analogies.

![Football](/images/cyber_manchester_2024_football.jpg)

Talking of difficult subjects, Jen Williams's talk was about that message that everyone dreads:

![Worst Day](/images/cyber_manchester_2024_worst_day.jpg)

Interestingly, she has observed that the cyber landscape has changed, it is no longer so much about prevention but 
more about recovery. "Assume breach" is now the default position.

That got me thinking, we've got a:

- Business Continuity Plan (how would we work without that systems?)
- Plan to preserve evidence (how do we prove what happened?)
- Recovery Plan (how would we bring that system back online?)

Right? RIGHT?

After I missed a few talks to spend time to have some interesting conversations on the vendor floor (I did love the
AI scepticism from people who were working for companies that were pushing AI solution), it was back to talks.

# Mean Time To...

I really enjoyed Callum O'Brien's talk about measuring your SOC. He suggested that we should measure

* Mean Time To Acknowledge (MTTA)
* Mean Time To Investigate (MTTI)
* Mean Time To Resolve (MTTR)

when looking at SOCs. He then proceeded to give some (anonymised) examples:

![Mean Times](/images/cyber_manchester_2024_mean_times.jpg)

These real world examples also illustrate an interesting pitfall when looking at metrics. In the above image you notice
that the MTTR is fairly close together, but there is a big variance in MTTA and MTTI. Why? Callum explained that it 
is likely all to do with SLAs. Most MSPs will work with a Service Level Agreement of under an hour. Some will be
measured against when they first acknowledge an issue, for some the SLA is related to when it is investigated. Just
goes to show that targets are often fudged. I did like Callum's call for everyone to publish their metrics, so that
we can learn from each other rather than having to rely on back channels.

# How to Hack a Security Conference

Edward Skraba talked to us about threat surfaces and an attacker's point of view. I especially liked this quote:

> You can't protect what you're not aware of

And then sat up in my seat as Edward proceeded to show us how we would hack the [conference website](https://cybermanchester.events).
It was really interesting what small bits of information can provide an interesting attack surface.

Hacking an infosec conference website:

![A bold move](/images/cyber_manchester_2024_a_bold_move.jpg)

# Privacy!

Good thing Edward repeatedly stressed that no sensitive information was exposed. The next speaker probably wouldn't 
have been too impressed. That was Daniel Selman, who is Data Protection Officer for the National Lottery and talked
about how he managed to transform a culture of fear of GDPR into an organisation that will happily send him emails about
possible data leaks and involve him early enough in conversations. He sees the role of DPIA as enabling rather than coming
from a "department of no". Sounds very agile to me - funnily enough I [very much agree]({{< ref "appsec_loves_agile.md" >}}) 
with that stance.

![Privacy](/images/cyber_manchester_2024_privacy.jpg)

# Quote of the Day

Patrick Grillo provided the quote of the day in his talk about the changing nature of security operations.

> Silver bullets only work on werewolves, not in security

I also liked the fact that even though Fortinet was betting big on AI, Patrick joked that he was

> Contractually obliged to mention AI

but it won't solve anything on its own. Might help with productivity though.

![Leveraging AI](/images/cyber_manchester_2024_leveraging_ai.jpg)

# Unbearable Positions

The next talk was by the hosts of the security podcast is [Compromising Positions](https://www.compromisingpositions.co.uk).
I really like the premise of interviewing non-infosec people about what they think about security. Lianne Potter and 
Jeff Watkins talked about "Wearable, Shareable and Unbearable" and was a tour de force about the impact of technology
in a world where the number of voice assistance has recently overtaken the number of humans in the world. They posited
that the social contract is changing. Whereas previously we surrendered some of our freedoms so that the state can take
care of us, now we live in an era of permissive sharing, where we surrender our data in exchange to be able to participate
in modern life online. That's quite something, but it really feasible anymore to opt out of the 24-hour monitoring, constantly
sharing locations or even being filmed by others? [Chatham House rules](https://en.wikipedia.org/wiki/Chatham_House_rule) 
probably means 15 minutes of disabling tech. What happens in Vegas definitely doesn't stay in Vegas anymore.

![You maniacs used AI](/images/cyber_manchester_2024_maniacs_ai.jpg)

The worrying thing really is that all of this usually happens without informed consent. Who actually reads 154 pages of
terms and conditions. And those conditions change. I don't feel comfortable giving Elon Musk access to my brain
with an internet-connected neural chip. Or have a search engine analyse all my habits to push more "relevant" product
to me. I am obviously ok with everything Apple does, because the walled garden is brilliant and never wrong!

Personally, the combination of [AI generated hallucinations]({{< ref "apocalyps_ai.md" >}}) with the ubiquity of 
data generation and the [enshittification](https://doctorow.medium.com/social-quitting-1ce85b67b456) of the major 
tech players, I guess the question (much like with climate change) whether it isn't already too late.

# Conclusion

It's not all doom and gloom though! It was great to encounter a lot of critical views about AI (accelerated energy waste
anyone?), but at the same time enough pragmatism to consider the possible benefits to productivity. The other themes
that emerged were metrics, the importance of communication and security as an enabling function. It is really important 
to measure and validate what we're doing, not blindly 
[worrying about toothbrushes](https://grahamcluley.com/the-toothbrush-ddos-attack-how-misinformation-spreads-in-the-cybersecurity-world/) 
and to do it without becoming the "department of no". 

Shame I had to say "I'm out" to miss the traffic when Deborah Meaden came on stage! Many thanks to Sarah for organising
a brilliant day, even if I got confused by the quantity of keynotes ;-)