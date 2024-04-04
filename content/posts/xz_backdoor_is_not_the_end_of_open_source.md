---
title: "XZ Backdoor: Not the End of Open Source"
date: 2024-04-04
tags: ["open-source","security"]
featured_image: "/images/xz_backdoor_is_not_the_end_of_open_source_title.jpg"
---

When I stumbled across a [post](https://www.openwall.com/lists/oss-security/2024/03/29/4) that an encryption library 
offers a potential backdoor to SSH connectivity on Good Friday, my first thought was: why is it always on a Friday that 
these things drop? And then my second one: oh bugger, here goes my weekend. Now, I won't go into the technical details, 
there are [many](https://boehs.org/node/everything-i-know-about-the-xz-backdoor), 
[many](https://jfrog.com/blog/xz-backdoor-attack-cve-2024-3094-all-you-need-to-know/),
[many](https://doublepulsar.com/inside-the-failed-attempt-to-backdoor-ssh-globally-that-got-caught-by-chance-bbfe628fafdd), 
[many](https://research.swtch.com/xz-script)
better resources out there, but I can't help thinking that this would/should force the software industry to think.

A scenario akin to the Baltimore bridge disaster was averted by the heroics of [Andres Freund](https://mastodon.social/@AndresFreundTec/112180083704606941) 
a Microsoft engineer who was surprised to see the SSH performance go down and decided to investigate. We all
owe this man a beer, otherwise this could have been so, so much worse:

![Baltimore Bridge](/images/xz_backdoor_is_not_the_end_of_open_source_baltimore_bridge.jpg)

If this hadn't been caught, potentially quite a few linux distributions would have had a backdoor. And considering that
patching is important, this would have made it quite a few distributions across the globe, giving the attacker almost
unprecedented access.

![Scooby Doo](/images/xz_backdoor_is_not_the_end_of_open_source_scooby_doo.jpg)
(credit https://mas.to/@swelljoe/112180840045810652)

I too experienced a moment of panic:

```
% brew info xz
==> xz: stable 5.6.1 (bottled)
General-purpose data compression with high compression ratio
https://xz.tukaani.org/xz-utils/
/opt/homebrew/Cellar/xz/5.6.1 (166 files, 2.7MB) *
  Poured from bottle using the formulae.brew.sh API on 2024-03-14 at 15:35:10
```

Thankfully, indications are that even though I had the backdoored lib on my machine, it wouldn't have been exploited. People
using homebrew and Kali (oh the irony) were exposed to the vulnerable lib too, as this is a Remote Code Execution that
needs to be combined with several triggers, I'm banking on it not having rooted me.

Now, this attack was possible because like so many open source projects, there was a sole maintainer that was 
providing support and updates for this library. Obligatory XKCD:

![Modern Infra XKCD](/images/xz_backdoor_is_not_the_end_of_open_source_modern_infra_xkcd.jpg)
(credit https://xkcd.com/2347/)

Now, if Log4shell has taught us anything, maintaining an open source package that a lot of modern infrastructure depends
on is a thankless task, not profitable and very often a labour of love. The maintainer was pressured into allowing access 
to someone else and is blameless in this ([context](https://x.com/thegrugq/status/1774392858101039419)).

Undoubtedly, as a result of this, a lot of security snakeoil salesmen will crop up that will promise us that SBOM 
solutions will fix all this and "buy our product, we don't use open source". I've seen evidence of this happening
on LinkedIn already (intentionally not linking to it as I don't want to amplify). Let me describe why this is 
a lot of bollocks:

# Stop using open source?

Should we stop using open source? How would that look like? Let's say that every open source library we're using
charges us $1 a month. This would solve the problem of maintainers not getting paid! 

Let's see how that works for your Javascript project with 3,000 transitive dependencies.

![NPM black hole](/images/xz_backdoor_is_not_the_end_of_open_source_npm_black_hole.jpg)

Besides from the fact that this would be quite expensive, just think of the administrative overhead of creating
commercial relationships with 1,000 suppliers! 

I also think that a lot of open source maintainers wouldn't actually want that commercial relationship. The log4shell
vulnerability has been full of stories of unwarranted expectation by businesses that 
[stuff needs to be fixed NOW](https://dev.to/yawaramin/the-human-toll-of-log4j-maintenance-35ap). Neither do the 
the tech giants want to pay more than they can get away with. You only have to have a look at the volunteers at 
[FFmpeg being told that an issue is high priority](https://x.com/FFmpeg/status/1775178803129602500) from Microsoft 
without having a support contract in place.

![NPM black hole](/images/xz_backdoor_is_not_the_end_of_open_source_about_the_money.jpg)

Funny how, when open source companies do want to change their licence to monetise their work, tech giants are more
than ready to fork projects and pour in resources. Like recently, [Redis](https://techcrunch.com/2024/03/31/why-aws-google-and-oracle-are-backing-the-valkey-redis-fork/).
I don't think this is about supporting OpenSource software, but about providing SaaS solutions based on Open Source.
[MongoDB](https://www.mongodb.com/legal/licensing/server-side-public-license/faq) and
[Hashicorp's terraform](https://www.hashicorp.com/license-faq) are other examples where companies tightened
the licence to protect itself from hyperscalers just eating their lunch.

# Let's normalise sponsoring

Lots of open source projects have maintainers. For example, when Lightbend handed over support of the 
[Play Framework](https://www.playframework.com) to the community - to focus on their own paid offering, I presume -
they struggled to keep funding for a single full-time developer. A lot of businesses and organisations rely 
it on to deliver software quick. I know, I was with [one of them]({{< ref "making_software_quickly.md" >}}).
Sponsorship looks easy. Just go to [OpenCollective](https://opencollective.com/playframework) and donate.
But what was frustrating was how difficult it was to navigate the corporate accounting guidelines to get
sponsorship off the ground. We couldn't do it via the credit card - the amount was too much. We couldn't get a purchase
order, because it would have had to have gone out to tender. In the end, the vast amount of value that was
provided that single OpenSource project was just used for free. I still feel rotten about that.

# What would happen if Open Source died?

For me one of the best things about open source is the visibility that it creates in how things work. If there is
an issue, I can start debugging and can step into the library code. If I had to create a support request every time
something went wrong, I'd never be able to solve any problems. Or if I had to ensure a commercial relationship
every time I wanted to ask for support. 

What would inevitably happen is that people would code their own libraries and frameworks again. And who wants that?
I remember when every web project ended up people creating their own personalised framework "to make things simpler"
which invariably ended up an unmaintainable, bloaty mess full of security holes. There's a reason why open source is
a good thing, because people can (and should) submit pull requests to fix issues!

Due to the immense usefulness of open source software, I ~am sure~ would hope that open source does NOT die.

# Next Steps:

* Buy Andres Freund a beer next time you see him.
* Have an inventory of your open source dependencies and vulnerabilities AND keep reviewing.
  I know that's [not easy]({{< ref "curating_vulnerabilities.md" >}})
* Don't demand fixes from open source maintainers. Try to help them and understand their situation.
* As a company, sponsor an open source project. It is in your interest that libraries are well supported. And don't 
  expect an RFP process or purchase order.
* Have a hack day, where you look at some of the dependencies you're using and whether some of your developers could fix
  some outstanding issues. If you can't find an issue tracker, you probably shouldn't be using that dependency!


