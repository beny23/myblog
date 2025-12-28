---
title: "Weakly Link 25/52"
date: 2025-12-28
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_52_title.jpg"
---

To those who celebrate the festivals either end of the last 7 days of the year: Happy \<insert here\>! This time
round, there's not one, but two bleeding fails in security, some interesting protections, how slowing down
is not only speeding up, but also making things more enjoyable. So without further ado, and much less waffle,
let's jump right in:

# GPG fail

Our [first fail](https://gpg.fail) is from the 39th Chaos Computer Congress gathering, which provided a 
rather bountiful set of vulnerabilities and weaknesses around GPG - the GNU Privacy Guard to give it its full title.
It is a rather late entry to my compilation, but it should be rather worrying that the presentation
started with demonstrating booting up an ISO image that had the correct signature as a Fedora standard image
but was something completely different.

The main takeaway is not that the cryptography is broken, but that the implementations had weaknesses. As an example:

An extra data packet can be used to append additional text to an already signed message that doesn't change
the signature and gpg reports "Good signature". 

To top it all off, there was a demonstration on how running gpg in an ANSI capable terminal (one that can do colours,
so pretty much all of them) can be used to trick a user to run any command.

Yikes.

I'm still digesting the implications here, but one thing that I'm wondering about are all the secrets in
configuration that are secured by GPG, wonder whether it's possible to use a similar extra data packet there to
modify config without changing the signature...

# Mongobleed

But, because we don't want to just have one Christmas present, there's another vulnerability that deserves 
a mention. I found [Kevin's piece](https://doublepulsar.com/merry-christmas-day-have-a-mongodb-security-incident-9537f54289eb)
a good overview of Mongobleed. Basically, for Christmastime the old stories remain the best ones: if you
have a mongo database exposed to the public internet, you will have to do some work to secure it.

> I’ve validated said exploit is real, you can just supply an IP address of a MongoDB instance and 
> it’ll start ferreting out in memory things such as database passwords (which are plain text), 
> AWS secret keys etc. The exploit specifically looks for those class of credentials and secrets, too.

# Speaking of Doom and Gloom

[This piece](https://www.irishtimes.com/world/us/2025/12/12/its-surreal-us-sanctions-lock-international-criminal-court-judge-out-of-daily-life/) 
in the Irish times about International Criminal Court judges caught my eye. Basically, there are judges
and staff of the court that are doing their jobs that have been sanctioned by the US.

> Prost had been added to the United States’ sanctions list, because in 2020 she ruled to authorise an 
> investigation into possible atrocities in Afghanistan, including by US troops. Amazon, obliged to implement 
> the sanctions as a US company, had cancelled her account.

> “You immediately lose your credit cards – it doesn’t matter where they were issued or by what bank,” Prost 
> said. Bank transfers can be challenging: a sum of money she tried to send a young couple as a wedding gift 
> has been lost in the system for weeks.

> “Online shopping becomes excruciatingly difficult, if not impossible. But there’s other things, everyday 
> things, like ordering an Uber or ordering tickets for something, or booking a hotel.”

This it demonstrates quite a different kind of supply chain attack. With so much of our daily lives controlled 
by corporations based in the US, and the Trumpian regime engaged in revanchism, it's becoming more and more 
apparent that relying on American corporations carries a certain risk. I for one would be right royally buggered
if Apple decided to suddenly close my account or I suddenly couldn't use my credit card anymore. 
I think Kafka would have a field day.

# Securing Supply Chains

On to more positive news, I think [this project](https://github.com/chainguard-dev/malcontent) by chainguard
deserves a look:

> malcontent's most powerful method for discovering malware is through differential analysis against CI/CD 
> artifacts. When used within a build system, malcontent has two significant contextual advantages over 
> a traditional malware scanner:
> 
> * Baseline of expected behavior (previous release)
> * Semantic versioning that describes how large of a change to expect

I've long thought that if someone manages to pull apart the changes in libraries and flag when there is unusual
changes, it would be of immense help. 

Of course, the problem is one of scale - there's so much out there that it would add to the workload of
Application Security, but I do think this would be one of the few times when I think the pipeline should break
the build if there's a patch-level library upgrade that suddenly introduces a lot of new functionality.

Of course, it's never simple. As malcontent is using YARA rules, it often gets flagged by existing antivirus
programmes. Oh, the irony.

# Your CSRF protection is old fashioned

[This post](https://blog.miguelgrinberg.com/post/csrf-protection-without-tokens-or-hidden-form-fields) 
by Miguel made me sit up. Miguel maintains the Microdot framework and he was adding CSRF protection when
a random internet user asked for modern CSRF protection rather than the old-fashioned token and cookie
based ones.

> The so called "modern" method to protect against CSRF attacks is based on the Sec-Fetch-Site 
> header, which all modern desktop and mobile browsers include in the requests they send to servers. 
> According to Mozilla, all browsers released since March 2023 have support for this header.

The advice has recently made it into the [OWASP cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#fetch-metadata-headers)
and is quite a bit simpler CSRF protection. And simpler is indeed better. And hopefully we no longer
live in a world where we need to support browsers that are 2 years out of date. Side eye to enterprise
software...

# Go back to go forward

Talking of simplicity, I really enjoyed [this post](https://blog.helix.ml/p/we-mass-deployed-15-year-old-screen)
about how sending a JPG a second was more reliable and less bandwidth intensive than video codecs for 
a solutation that need to share screen content. Sometimes the old and simple ways are better. I can't help
but feeling like this whenever I read something about React or whatever the latest clientside framework du jour
is. Me, I like HTML...

# Go slow to go fast

"Slow is smooth, smooth is fast" go the US Navy Seals. I very much enjoyed
[this take](https://www.raptitude.com/2025/12/maybe-the-default-settings-are-too-high/):

> Slowing down your rate of consumption will inevitably change what you want to consume. Reading 
> throwaway news articles or AI slop with great care and attention is only going to show you how 
> empty of value it is. Reading dense writing in inky old books, crafted for your mind by great masters, 
> becomes easier without the rushed pace, and the meaning just blooms out of it.

> Same with food. Try to savor a cheap, waxy “chocolate” bar, or a bag of store-brand cheese puffs, and you 
> discover a harsh taste that you don’t want to look at too closely. Enjoy a homemade pastry with great 
> attention, and discover there’s even more in it than you realized.

Maybe that's why I am looking forward to reading to the children almost as much as them. I can read
loud, pause for questions and really digest what's coming.

Will I do that in work? Don't be ridiculous, I don't have time for that ;-)

# Information from another age

When it comes to slowing down, I stumbled across [this piece](https://contingentmagazine.org/2019/03/25/mailbag-march-25-2019/).
It seems apt that this is from over 6 years ago and talks about how much information that humanity has
accumulated is not available on the internet and how much researchers still have to go to archives and
consult primary sources:

> U.S.’s National Archives and Records Administration (NARA), the independent agency tasked with collecting, 
> preserving, and making public government records, stated nearly 235 million pages of records have been digitized, 
> but only 15% of the digitized files are available to the public. NARA hopes to reach 500 million pages of 
> digitized papers, but that goal won’t be hit until fiscal year 2024, and while half a billion digitized pages
> is a lot, that’s out of a *total number of 12.5 billion pages of records*. That represents only 4% of all 
> government documents NARA has in their collection.

Now, this is from 6 years ago, but I don't think much has changed. Good thing we're practicing how to
research information without it being online and not just asking ChatGPT these days.

# Merry Christmas

To close, I very much enjoyed this McSweeney's reimagined take on Kevin spending Christmas at [home
alone](https://www.mcsweeneys.net/articles/home-alone-updated-for-the-age-of-surveillance).

That was the weakly link, goodbye.