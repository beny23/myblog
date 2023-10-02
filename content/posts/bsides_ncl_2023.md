---
title: "BSides Newcastle 2023: Chaos for the future"
date: 2023-10-02
tags: ["bsides","conference","security","appsec"]
featured_image: "/images/bsides_ncl_2023_title.jpg"
---

After BSides Lancs and [Leeds]({{< ref "bsides_leeds_2023.md" >}}), Newcastle was my third BSides, both of the 
year and ever. I got up early in the morning for a road trip from Preston to Newcastle, and setting off at 5:30 on Saturday
turned out to be atmospheric and straightforward. So I was a bit early and got a glimpse of the chaos that is
putting together a BSides. Later I found out that BSides Newcastle was traditionally more chaotic than some of the other BSides.
Now, even though the signs were hand-written at the last minute, the start was late and the schedule a little bit
random, this did not matter one bit. Who needs precision organisation, when you can have energy that is just on
the right side of chaos? And that is infectious.

![Me in an Apron](/images/bsides_ncl_2023_apron.jpg)

No T-shirts at NCL, no, let's have aprons. Orange for organisers, yellow for volunteers, pink for sponsors and
brown for speakers. Talking shit, eh?

One of the best comments of the conference was overhearing someone saying "looks like a shopkeeper 
convention". You're not wrong:

![Shopkeeper convention](/images/bsides_ncl_2023_shopkeeper_convention.jpg)

Why is there a guitar, you might ask. Well obviously for the opening conference singalong of "I've no more hacks
to give" to the tune of "I've no more fucks to give".

![No more hacks](/images/bsides_ncl_2023_no_more_hacks.jpg)

That's the right attitude. Considering that about a week before the con, the organisers had been left with
no venue and had to find a new place and still managed to put together a fab day is just testament to exactly that
attitude.

It didn't get any less sweary. And no, I'm not just thinking about Eliza's tale of vendor horror stories: 
 
- the one about the SIEM in a flat network structure, that got ransomwared with the rest of the company
- the one about the vendor that blackmailed their client to not sack them
- the one about the vendor that installs a SIEM but then doesn't plug it in
- the one about the phishing simulation that uses sexual abuse or miscarriage in pregnancies as a hook and then displays 
  a "Haha, you need to do" phishing awareness training" without any thought of how this might affect people
- the one about charging for doing log aggregation and then not doing anything other than storing the raw data

Though probably the best one came out of the audience where a phishing campaign got detected and warning emails were
sent out, including to the phisher themselves.

![Vendor horror](/images/bsides_ncl_2023_burn_bridges.jpg)

Can't not swear there.

# Oh shit

Probably not as bad as Lee's opening keynote with the story how when he checked the details on his DBS check, the details
of a convicted sex offender with weapons charges was returned because the name was a bit similar.

![Cert horror](/images/bsides_ncl_2023_cert_horror.jpg)

# Fuck me

The horror continued, as Vi gave an excellent overview of BGP. That's the protocol that holds the internet together,
that was designed on three napkins and has some pretty glaring security flaws that are still not completely fixed. Flaws,
which allowed an ISP in Turkey to bring down the whole internet for an hour, or have a Pakistani ISP mistakenly down
YouTube for everyone, or allow a clever campaign to steal cryptocurrency by pretending to be AWS Route 53. It's always
DNS... except when it is BGP.

![Pretend to be the entire internet](/images/bsides_ncl_2023_pretend_internet.jpg)

# My turn

I was gutted to miss Brian's talk about the Great Post Office scandal, you know the one where Fujitsu instead of owning
up to software problems, got lots of sub-postmasters convicted of fraud instead, but it was my turn to give my
talk on [Denial of Service]({{< ref "precision_munitions_for_denial_of_service.md" >}}). At the start of the 
session, I shook hands with the only two people that were there. Though there were a few more that came about halfway 
through, so I did a quick recap at the end. I loved it though. I probably would have given the talk to just a single 
person, though next time, I probably should sit down and turn it into a workshop!

# Patch This!

For the scran note (love the concept), Jerry went through the difficulty of fixing security dependency issues. There
were some fab stats in there, which I will undoubtedly steal:

- Only about 7% of CVEs get exploited
- Only about 12% of CVEs get patched

The problem is just the sheer volume. These days, on average 75 new CVEs get raised every single day!

![75 CVEs a day](/images/bsides_ncl_2023_75_cves_a_day.jpg)

In order to help the community work out what should be looked at, Jerry hacked together [patchthis.app](https://patchthis.app)
during a long-distance flight which aggregated a few good vulnerability intelligence sources and aggregates a CSV
file that contains CVEs that are being actively exploited according to [CISAgov](https://www.cisa.gov/known-exploited-vulnerabilities-catalog),
[Metasploit](https://docs.rapid7.com/metasploit/modules/), [Nuclei](https://github.com/projectdiscovery/nuclei-templates)
and [EPSS](https://www.first.org/epss/). And it's open source too. Bravo! This is right 
[down my alley]({{< ref "curating_vulnerabilities.md" >}}).

# Grow the industry

The closing keynote was delivered by Rosie, who is now organising four BSides, with an impassioned plea to do more
to help grow the industry, to make it easier for people to start out in Cyber. There's a lot of people with years and
years of experience, and the infosec community needs to do more to welcome fresh talent in. Hear hear!

![Infosec Barbie](/images/bsides_ncl_2023_barbie.jpg)

And brilliantly, the conference was fully of children! Complaints departments and little ones running around, playing
with battlebots, putting glitter and face paint on people. I thought that was really cool, maybe I'll bring my little
ones along next time. And it also, albeit temporarily, put a stop on some of the sweariness...

All in all, I loved the chaos, the undercurrent of anarchy and embracing the weird and wonderful. Don't ever change,
Boncl!

