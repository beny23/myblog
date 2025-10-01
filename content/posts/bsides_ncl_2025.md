---
title: "BSides Newcastle 2025: Mission to Cyberspace"
date: 2025-10-01
tags: ["bsides","conference","security","appsec","genai"]
featured_image: "/images/bsides_ncl_2025_title.jpg"
---

BSides Newcastle is probably one the most anarchic of the BSides I've been to so far. So much so that the fascists
organised a protest. Well, not really, but there was a far-right and counter-protest not far from where 
the conference was. Thankfully, the organisers were on top of it and kept is all up-to-date with advice: 
"Punch Nazis" (for the benefit of the tape, nobody endorsed violence).

Aaanyway, aside from a really early start I was ready to dive in and hope to share some of the experiences with you.

![Me, mission ready](/images/bsides_ncl_2025_mission_ready.jpg)

Yes, I needed more coffee! But, no time, [Dave](https://tautology.org.uk/blog/) dusted off his astronomy degree to
give us a [whistlestop tour of satellite communication](https://bsky.app/profile/beny23.github.io/post/3lzsootmers2c). 
Putting on my most Nimoy-Spock face, it was fascinating to learn that a lot of the satellite comms can be listened 
to with Software Defined Radio (SDR), drug smugglers have learned how to relay messages via satellites and
it's possible to patch Voyager 1 yourself as it's signals have no encryption - you do need a very powerful dish 
array though.

![GPS epoch](/images/bsides_ncl_2025_gps_epoch.jpg)

Also interesting was the fact that GPS has an epoch. Last time it happened 2019, causing some parking meters
to fail. GPS time is pretty weird. Anyway, the next time it'll happen is 2038. Now why does this date seem
[familiar](https://en.wikipedia.org/wiki/Year_2038_problem)?

# Top Secret

I really enjoyed the Top Secret talk. But obviously can't talk about it.

![Top Secret](/images/bsides_ncl_2025_top_secret.jpg)

# Beware of Insiders

The next talk was by [Donna](https://www.linkedin.com/in/donnagoddard13/) about 
[insider threats](https://bsky.app/profile/beny23.github.io/post/3lzsudm5f522q). There's a lot of
myths about which usually can be categorised into:

- won't happen to me
- it's a tech problem
- we can't do anything about it due to privacy

I think that talk was rather timely, especially against the background of this [BBC Story](https://www.bbc.co.uk/news/articles/c3w5n903447o)
about a BBC tech reporter being offered lots of money to give up credentials. Looking at a lot of the attacks in
the news recently ([JLR](https://www.theguardian.com/business/2025/sep/20/jaguar-land-rover-hack-factories-cybersecurity-jlr),
[Harrods](https://www.bbc.co.uk/news/articles/c8d70d912e6o), [M&S](https://www.theregister.com/2025/04/22/marks_spencer_cyber_incident/))
the weak point is all too often unwitting insiders.

The solution is refreshingly simple: treat your staff well: A pissed-off employee might be persuaded to
sell credentials. An overworked call centre agent might reset a password. And an unfairly laid-off employee might still 
have access to systems.

# Supply Chain Perfect Storm

Then I couldn't help myself nodding along to [Jerry](https://bsky.app/profile/jgamblin.bsky.social) talk about
the CVE Crisis:

![CVE Crisis](/images/bsides_ncl_2025_cve_crisis.jpg)

There's a real risk that due to "the political situation" over in the US that the NVD will no longer be able to score
CVEs. [And that is a problem](https://bsky.app/profile/beny23.github.io/post/3lzswi76oss2q). Presently, NVD can 
deal with about 80 CVEs a day. But 130 new ones are added every day. Something has to give.

And before you say AI - it's not good enough. Experiments so far have shown that it can be about 90% accurate
in a best case scenario. But for CVSS scoring that's not sufficient. I'm actually relieved that AI isn't being
stuffed down the NVDs throat here.

![Houston, we have a problem](/images/bsides_ncl_2025_houston_we_have_a_problem.jpg)

Jerry hopes that when he comes back to Newcastle next year, he'll have some better news. There's CISA and ENISA
looking to fill the void, but that in itself risks fracturing the ecosystem. Imagine a situation where the EU,
US, Indian vulnerability registers are all independent. We'll have to look at each and every one of them, aggregate
and prioritise. Of course, big vendors will provide solutions but what about OpenSource or smaller companies. Will
they be left out in the cold?

Sounds like a headache to me and I really appreciate the work he's doing in that area. 

# The Death of Learning

The [munchnote](https://bsky.app/profile/beny23.github.io/post/3lzt2tommgk2t) came from 
[Christine](https://bsky.app/profile/christinesmly.bsky.social) who talked about the
possible impact of using GenAI on our ability to think.

![Kill chain](/images/bsides_ncl_2025_kill_chain.jpg)

And thinking caps were definitely required! We covered how the earliest "technology makes us stupid" can be 
tracked back to Socrates and Plato, how biological and artificial neural networks (BNN and ANN) are similar but
completely different and how "it's the plasticity, stupid" (channelling my inner Bill Clinton there). 

If we delegate thinking to an LLM, then the problem isn't that we don't get the right answer, but that 
we will lose our ability to learn. Learning isn't just about learning content, it's about practicing learning.

Or put in another way "it's not the destination that counts, it's the journey".

Lots to think about - and no, I won't just ask ChatGPT...

# Operational Technology Hacking

The [next talk](https://bsky.app/profile/beny23.github.io/post/3lzt4fvd25k2t) I attended was 
from [Liam](https://www.linkedin.com/in/liammcgrathdigital/) about Operational Technology. These are the bits
that open doors, build cars, operate on people, etc. So when pentesting this kind of thing, there's a real
risk that shit will hit the fan. 

I loved Liam's golden rule of OT testing:

![When the fun starts](/images/bsides_ncl_2025_when_the_fun_starts.jpg)

This is quite important when a simple portscan can bring down infrastructure. Often the only way of pentesting
is to create simulation rigs and test in the cloud.

I did have to chuckle about his example about robotic arms being controlled by files being uploaded to an FTP
server. So not that different to legacy IT, then...

# Scam, scam, scam

I quite enjoyed [Clive](https://www.linkedin.com/in/clive-king-90a847/)'s talk about 
[Money Laundering](https://bsky.app/profile/beny23.github.io/post/3lzt6oqgsm22t). I do
wish I had an edit button on BlueSky though where I misnamed him Chris (sorry). Mind you, the title
of the talk was a bit misleading because he didn't teach us about how to launder money, which was
both disappointing and reassuring. 

I did like his incident maps about the root causes of being scammed. Porn or friends from the internet is an 
unsurprisingly common factor:

![Incident maps](/images/bsides_ncl_2025_incident_maps.jpg)

His top tips:

- "Different phones for fun and profit"
- "Adopt an elderly person"

# Me!

Then it was my go. I gave a live demo with LLMs and ZAP and OpenAPI. Read more 
[here]({{< ref "make_it_so_genai_and_zap.md" >}}). It was really good fun, the network didn't play up
too much and there were tons of questions and great feedback.

# Just do the fucking basics

After that, there was a great [locknote](https://bsky.app/profile/beny23.github.io/post/3lzth5hrem22y) by
[Kat](https://bsky.app/profile/rnbwkat.bsky.social) about cloud security.

The grand majority of cloud breaches happen due misconfigurations - and due to the usual suspects:

![Incident maps](/images/bsides_ncl_2025_usual_suspects.jpg)

There were some great stories about using cloud misconfigurations to DNS poison an AI start-up and MitM
the prompts and changing them. 

Top tip: as a customer never start a pentest engagement with "we're sure you won't find anything". That's
just one step down from proclaiming someone is "unhackable".

# To finish

It was a great BSides! I loved the vibe, the content of the talks. I missed out on lots of interesting talks,
and never got to the lockpicking, nor the battlebots, nor the CTF. I came away buzzing and full of thoughts. 

Congrats!

