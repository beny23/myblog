---
title: "Social network distance"
date: 2020-09-16T02:12:25+01:00
category: ["marketing"]
---

![Social Network Distance](/images/social_network_distance_title.jpg)

It's now about two weeks since the HMRCs splurge on getting the UK to go out for a 
meal has finished.  In [discount dining finder]({{< ref "/posts/discounted_dining_finder.md" >}})
I showed how to make a map-based tool to search all Eat Out to Help Out (EOTHO)
establishments in very little time.  

What follows is the story of what happened after the site launched, and how I failed to make a dent at 
marketing.

To recap:

* HMRC published a data-set of restaurants participating in EOTHO on [GitHub](https://github.com/hmrc/eat-out-to-help-out-establishments)
* I fed that data into a set of shell and AWK scripts to generate a searchable map
* The static site is hosted on GitHub

I was quite pleased with the result and wanted to shout about it.

Here's what I did:

# Slack (Success)

I sent out messages on the Equal Experts, HMRC Digital and X-Gov Slack instances and got some really nice feedback.

# Blog about it (Success-ish)

We turned my original blog post into an [Equal Experts blog-post](https://www.equalexperts.com/blog/our-thinking/eat-out-to-help-out-discounted-dining-finder/)
which I appreciated

# LinkedIn posts (Success-ish)

I took to LinkedIn and added a few posts, [my most viewed post](https://www.linkedin.com/posts/gerald-benischke-9811b663_help-out-eat-out-to-help-out-activity-6694598773173182464-TGY0)
just pointed back to my blog, had nearly 4000 views and 60 reactions.  Not bad considering I usually get hundreds of views
rather than thousands, but I'm not exactly influencing anybody.  Not that I particularly want to either...

# Open Source (Success)

I very early decided I would make the website open-source, and I was mainly thinking that it would be a good way
of promoting the site, but was very pleasantly surprised when not long after launch, Issues and Pull Requests started
appearing. I started collaborating with Scott Dutton (who I'd never met before) - and he was a great help bringing 
improvements and ideas.

# Learn something iOS (Success)

After my descriptions of my approach, I got together with Paul Stringer, a colleague from Equal Experts - again never
met IRL - such are these pandemic times - but we got chatting about how this could be turned into an app.  So we did
(well, Paul did most of the app - I just contributed a couple of server side bits).  Found it quite exciting to have an
App on the Appstore.  For more info on the app, read [Paul's blogpost](https://www.equalexperts.com/blog/our-thinking/how-i-created-a-mobile-app-to-help-you-find-where-to-eat-out-to-help-out/)

# Learn something Android (Fail-ish)

After the success with getting an iPhone app out of the door (about two weeks into EOTHO), I was talking with my colleague
Chris Sawczuk about doing an Android app.  And while it was a bit lagging behind (and it took me probably too much time
getting the Google accounts sorted), we had one ready to go - it just never made it out of the door as we never quite
got the internal test version released before Eat Out to Help Out actually finished. 

# Interlude on numbers

What kind of traffic was I getting?

![Website Stats August](/images/social_network_distance_website_stats.png)

The initial numbers looked great - I was getting thousands of users, without more than just word of mouth.  However, 
I quickly discovered that most of my traffic wasn't direct traffic but rather because first deeside.com and then wrexham.com
had embedded my map in an iframe in their site (which I was quite happy about, first as I released the code as open
source and secondly and especially as deeside.com ran a bit of background based on my initial blog post - great!)

So I was quite encouraged and thought - let's put a bit of effort into marketing and see where it goes.

# Submit to HackerNews (Fail)

I submitted my website to [Hacker News](https://news.ycombinator.com/news) which has a section on "Show HN"
where submitters can show "stuff".  I never got a single upvote.  Probably I only just created the account and have no
"karma"

# Tweet about it (Fail)

I sent out tweets to Martin Lewis, tech journalists, my local MP, influencers that tweeted about Eat Out to Help
Out, replied to announcements about Eat Out to Help Out. All in all, it was quite the failure.  Probably because I
created my twitter account 10 years ago, and never really used it.  I managed to double my followers - now
I have the grand total of 9 followers and while it is good to find some really useful nuggets, there's just so much
noise to wade through. 

# Facebook (Fail)

My facebook profile is about as unused as my twitter one.  So it wasn't all that surprising that sharing some news
about my new website came across as a bit funny.  My wife was more successful in spreading the news about my website.

# Reddit (Fail)

Reddit was a funny one.  I suffered from just the same problem as Hacker News, I created the account, shared something
and nobody wanted to know.  It was even funnier when my post was deleted as duplicate!  I questioned it with a moderator 
and the reply was as swift as it was crushing:

> Generally (with a certain level of exception), we don't advertise apps/sites/projects/etc.
> The flairing was incorrect though. That was my fault. It should have been marked as 'spam'.

Fair enough, I am coming to the realisation that unless getting really lucky, going viral on "the Interweb" can only
be done if you have lots of followers/karma/points or know someone with lots of followers/karma/points.

# Notifying the press (Epic Fail)

My last thought was to send out emails to national and local newspapers.  My thinking was that local papers might 
be interested in a story along the lines of "Local man writes software to find Eat Out to Help Out restaurants" and
for the national newspapers, technology or restaurant trade publications it would have been interesting to show
an alternative "Search Eat Out to Help Out" website, which complementing the HMRC tool, IMHO offered some advantages.

But nobody replied.

Well, in what I regard as somewhat of an achievement, the Editor of theregister.com replied with

> On second thoughts, I won't waste your time. I don't think it is something we'd cover.
>  
> Thanks for the offer and best of luck with it.  

This little project has taught me a lot of technical and non-technical lessons, was a lot of fun
and who knows what can be done with the underlying tech in future.  My main takeaway is: Marketing is hard! It's
not about what you know, it's how many followers you have ;-)