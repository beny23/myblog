---
title: "The Case Against Automatic Dependency Updates"
date: 2023-04-21
tags: ["appsec","security","dependencies","scale"]
featured_image: "/images/automatic_dependency_updates_title.png"
---

The question of automatic dependency updates came up in our Slack channel the other day. There was a lot of nodding
on how it is a good thing. Tools like [Dependabot](https://github.blog/2020-06-01-keep-all-your-packages-up-to-date-with-dependabot/)
and [Renovate](https://docs.renovatebot.com) were mentioned. Yet I was a dissenting voice. Why?

The case for automatic dependency updates is simple and seductive:

* A bot would automatically scan your dependencies in your source code and create pull requests to update your libraries
  to the latest versions, sometimes even automatically merging it in.
* "it helps keeping things up to date a lot quicker"
* "it fixes supply chain problems quicker"

Now what is wrong with that? How could I not want to keep my dependencies up-to-date? Most of my work is in
[AppSec](/tags/appsec/) after all! I can practically hear the shouts of "Heretic!"

![Not updating your dependencies? Burn Him!](/images/automatic_dependency_updates_witch.png)

Furthermore, I've previously shown what a pain [keeping on top of dependency vulnerabilities]({{< ref "curating_vulnerabilities.md" >}})
is. Why not just update everything automatically and be safe automatically?

I think the following obstacles need to be overcome:

# Continuous Integration

First of all, automatic dependency updates do require mature delivery pipelines. It's no good automatically updating
your dependencies if building your system is a bottleneck. Or if the quality of your automated tests would not catch
those subtle bugs that might be introduced because a minor library update pulls in a transitive dependency that is incompatible
with another library and it sails through to production and suddenly things start breaking.

It also means that long-lived feature branches are out. You wouldn't want your colleague to work on a new feature 
only to find that in the meantime a few libraries have changed and all their testing has gone out of the window.

Hold on a minute! [Continuous integration](https://martinfowler.com/articles/continuousIntegration.html) is a good 
thing. And [long-lived branches are a bad thing](https://thinkinglabs.io/articles/2021/04/26/on-the-evilness-of-feature-branching.html).
What about my dissenting voice? Well, good practice does not happen everywhere. I've experienced plenty of places where
working in this manner is still alien. GitFlow is still abundantly practiced despite it being a [bad idea](https://youtu.be/_w6TwnLCFwA).

If you're unlucky enough to be in one of those places, automatically updating dependencies might not be a good idea.

# Continuous Delivery

So let's assume you are building your software quickly. What about releasing it? How easy is it for you to get
those automatically updated dependencies into production? Does it happen as part of your pipeline? Are you practicing
[You Build It You Run It](https://www.equalexperts.com/blog/our-thinking/what-is-you-build-it-you-run-it/)? Or
is there a complicated bureaucratic release process? Do you have to book a slot with the Change Advisory Board (CAB)
and get the green light to release a new version of your software from "the business". Or do you have change freezes
during "Key Business Events"?

In such places it might not be the best idea to push for automatically updating dependencies. 

# Maintenance at Scale

Let's assume that you are continuously building, you have good automated tests, and can deploy quickly and without
paperwork. Sounds like bliss? But what if your team is looking after 100 services? This is quite common in software. All
too often, services are created by project teams that crank out feature after feature and then the team gets disbanded
or moved on when the project is finished and the service enters maintenance mode. But software is
[never finished](https://www.stevesmith.tech/blog/the-maintenance-mode-myth/). Someone has to look after it. Fix bugs.
Make small changes. Implement new requirements. And update dependencies.

It might sound tempting to just automate the dependency updates, but I think that at scale it is not feasible to make
lots of changes just because a dependency gets updated. If there is that subtle bug that might only found in
production, overworked live teams often don't get the time to monitor all the deployments and look for signs of things 
subtly going wrong.

Automatically updating dependencies might not be such a good idea then.

# Legacy software

Commonly the software we're looking after is old. With dependencies that are out of support and no longer updated.
It should be added that "old" does not mean "bad". There is a lot of ancient software at the heart of government,
defence, banks, insurance and retail operations. It's usually those services that makes money or are the most critical
for successful operations.

How do you update dependencies then? All too often, the only way to fix a vulnerability is to change the dependency to 
a new major version or a completely different library. Automatic updates do not help there.

# But but but security?

What about security? And supply chain attacks? I'm in the [blue team](https://snyk.io/blog/red-teams-vs-blue-teams/). 
Surely I want to update as quickly as I can?

There's a brilliant illustration of the problem of dependencies:

![Heaviest objects in the universe: Node modules](/images/automatic_dependency_updates_black_hole.png)

But it isn't just Node.js services that pull in a ridiculous amount of libraries. At the time of writing, I am currently 
in a Scala shop and I counted 438 JAR files in one of our services. Dependency hell is not just a special place
for Javascript developers.

In order to navigate this hell, it is important to realise what impact vulnerabilities can have. In 
[Curating Dependency Vulnerabilities]({{< ref "curating_vulnerabilities.md" >}}) I show how that many vulnerabilities
are false positives. In [on the weaponisation of open source]({{< ref "on_weaponisation_of_open_source.md" >}}) 
I explore how automatically updating can have negative impact without it being traditional supply chain poisoning.

# Update with Intent

This is why I think that software should be updated with intent. It should be a positive choice to make an update to a 
dependency. Otherwise, I think there is a risk that complacency creeps in. If there's a system in place that
updates my dependencies automatically, how likely am I to investigate the supply chain and miss vulnerable
transitive dependencies for which there is no fix yet?

There's another point, I actually don't think the automated tools that update dependencies save that much time. 
I mean how difficult is it really to go to your repository, find the library definitions in `package.json`, `build.sbt`
or `pom.xml` and update a couple of version numbers. The code change is a 5-minute job. Especially when github allows
editing files and creating a PR from within your browser. It's all the other bits, the testing,
the verification, the paperwork, the deployments that take time.

Note, I am not a Luddite. I am all for automation. But I'd rather automate the detection of possible problems rather
than the fixing of it, because more often than not, the fix is never that simple.

For an alternative viewpoint, I highly recommend reading 
[Automating dependency updates: the big debate](https://www.cosotateam.com/post/automating-dependency-updates-the-big-debate)

What is right for you? The answer - as with so many things in Software Engineering - is inevitably "it depends".
