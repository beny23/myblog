---
title: "On the weaponisation of open source"
date: 2022-03-18T14:30:32Z
tags: ["my-take-on","security","open-source"]
featured_image: "/images/on_weaponisation_of_open_source_title.jpg"
---

First of all I need the preface this article on how much I abhor the Russian invasion of Ukraine and I wholeheartedly 
support the sanctions.  However, I think the conflict has spilled over into areas of software development that have 
got some unintended consequences attached.

As part of this post, I'm going to look at

- the decision by [MongoDB to cut off services in Russia](https://www.theregister.com/2022/03/15/mongodb_terminates_russian_saas/)
- the destructive change in a node library that [deleted files on Russian IPs](https://github.com/RIAEvangelist/node-ipc/issues/233#issuecomment-1068182278)
- a change in code/licence in a community terraform module to assert that [Putin is a dickhead](https://github.com/terraform-aws-modules/terraform-aws-ec2-instance/commit/6867788411a202b61187f9935e9eaa72a18f0bbe)

# MongoDB cutting off Russian customers

MongoDB is a company and in order to comply with sanctions they have decided to cut off Russian customers.  I think 
there's a bit of inconsistency in the tech sector on whether a SaaS offering and paying a subscription is tantamount to
a new sale, but I think that is in spirit of the sanctions - making it difficult for Russian companies to operate.  
Mongo is a commercial entity and as such can choose who to sell their wares to.  I don't think there's any restrictions
on running an instance of Mongo in your own kit.

While I agree with this, it does pose an interesting question.  What would happen to your organisation if a service 
provider disappeared?  Note, I do not think that this means that we should all rush to building our own data centres, 
writing our own databases and running all our own services.  The simplification and optimisation of using Software *as a
Service* should not easily be dismissed.  However, it does not hurt to be prudent and do a threat assessment of what
would happen if the service disappears.  Again, I do not think that this should be taken as an incentive to run everything
in multi-cloud, as I happen to think the overall increase in complexity would actually reduce reliability.

# Node library deleting all files on Russian IPs

A bit more sinister is [this story](https://www.theregister.com/2022/03/18/protestware_javascript_node_ipc/) - under the
moniker "protestware" the `peacenotwar` dependency was injected into dependencies that affected vuejs cli (and unreal unity
by some reports).  `peacenotwar` checks the IP address of the computer it is running on, and if it is deemed to be inside
Russia, deletes all files.

Now, supply chain attacks against node modules is nothing new.  Not many months go by without a story about a 
[hijacked node module](https://news.sophos.com/en-us/2021/10/24/node-poisoning-hijacked-package-delivers-coin-miner-and-credential-stealing-backdoor/)
installing backdoors or cryptominers.  I think it is safe to say that everyone agrees that these attacks are malicious
and the actions are those of criminals.

With this idea of "protestware" it becomes morally a little bit ambiguous.  I think this feels a little bit of a problem
of recent times where because of Brexit or Trump, political discourse has turned to be very divisive and tribal.  You
are either with us, or against us.  In my mind, the term "protestware" is attempting to legitimise the malicious actions
and very much turns open source libraries into weapons to be aimed and fired at your opponent.  

I do think that these
actions are to be condemmed - especially as the "delete files based on geofencing IP addresses" has got the potential of
causing collateral damage.  I don't know how much truth there is in the issue raised on the `peacenotwar` repository that
an American NGO lost 30,000 files documenting Russian war crimes - but it should be remembered that geo-location is not
always right.  Anecdotal evidence of misidentification of IP addresses makes this a very indiscriminate weapon.  And I 
have to ponder that if mass-bombing of civilian targets by Russia's armed forces is to be abhorred, blanket wiping
files in the Russia IP range is not exactly a targeted action.

# Putin is a dickhead

Last but not least, I'd like to look at a story that community terraform modules for AWS had been update to include
make political statements.  First there was a change to the licence "Additional terms of use for users from Russia and Belarus":

> By using the code provided in this repository you agree with the following:
> * Russia has [illegally annexed Crimea in 2014](https://en.wikipedia.org/wiki/Annexation_of_Crimea_by_the_Russian_Federation) and [brought the war in Donbas](https://en.wikipedia.org/wiki/War_in_Donbas) followed by [full-scale invasion of Ukraine in 2022](https://en.wikipedia.org/wiki/2022_Russian_invasion_of_Ukraine).
> * Russia has brought sorrow and devastations to millions of Ukrainians, killed hundreds of innocent people, damaged thousands of buildings, and forced several million people to flee.
> * [Putin khuylo!](https://en.wikipedia.org/wiki/Putin_khuylo!)

In addition, this acceptance was included in the code:

```terraform
variable "putin_khuylo" {
   description = "Do you agree that Putin doesn't respect Ukrainian sovereignty and territorial integrity? More info: https://en.wikipedia.org/wiki/Putin_khuylo!"
   type        = bool
   default     = true
}
```

Setting the variable to `false` means the terraform module would not work.

Now, I think this is problematic on two fronts:

## I don't think this can be classed as open source anymore:

The definition of an [Open Source License](https://opensource.org/osd) is quite clear:

> 5. No Discrimination Against Persons or Groups
> The license must not discriminate against any person or group of persons.

I don't really want to have to read through each of my dependencies and transitive dependencies licences to determine
whether I am agreeing to discriminatory terms by using a library.

Where does it end?

- What about a licence that demands you refrain from/support eating meat?
- What about a licence that demands you are pro-life/pro-choice?
- What about a licence that demands you vote Democrat/Republican?

I think it is quite undesirable to politicise/weaponise open source that way.  Depending on what kind of organisation
that you work with, it might be completely unacceptable and outside the permissions for an engineer to agree to these
kind of contracts.  I can't imagine that a government agency would want to discover that the software their using
is mandating some kind of political stance.

## I don't think this can be enforced anyway

[This article](https://www.zdnet.com/google-amp/article/you-cant-open-source-license-morality/) argues that it is not
practical to encode morality into licences, as it would either be ignored or forked anyway.  The JSON licence 
"The Software shall be used for Good, not Evil" is unenforcable, and the licences are designed with clause 6 in mind:
"No Discrimination Against Fields of Endeavor" in order to avoid licence traps from downstream dependencies.

In my opinion, the result of the `putin_khuylo` change is that this terraform AWS module can no longer fulfil either of
those clauses and therefore can no longer be classed as open source.

The author of the change discussed this on [Hacker News](https://news.ycombinator.com/item?id=30710032) and it has since
[changed it](https://github.com/terraform-aws-modules/terraform-aws-ec2-instance/commit/64d99323448058194ed9ce263f1e372630c30ce2) 
to be "Additional Information" rather than "Additional Terms and Conditions" - but the `putin_khuylo` code change
remains in the module.

In my opinion that raises eyebrows (if not red flags) about the "safety" of these components.  It looks like these changes
were made straight into the `master` branch without pull requests - that does suggest a lack of review process. These
actions have negatively impacted the trust in the maintainers.  And that makes me wonder whether using those
components is safe.

Furthermore, from a licencing perspective, some organisations have guidelines of what licences are allowed, so if 
it can be demonstrated that the code breaks the clauses of the license, would it still be safe to use it?  Some 
war stories about frantically removing "infectious" GPL'd libraries make me think the lawyers might have a field day.

# What to do?

Now, if that trust is gone, there are only the following options:

- Find a different library  (easier said than done)
- Manually review the code for every commit (that means a lot of work - might not be practical if the licence has changed)
- Fork the library (maintaining libraries is expensive and a huge commitment)

The problem with dependencies in modern software engineering is that only the biggest organisations have the resources
to write all their own libraries (e.g. [Google](https://www.quora.com/Why-does-Google-internally-overwhelmingly-use-its-own-in-house-developed-software-and-nearly-no-externally-originated-opensource-or-proprietary-software), 
other organisations such as Goldman Sachs fall into that category too).  Most organisations simply do not have
the capacity to write everything from scratch - and for good reason:  The whole point of open source is collaboration
and re-use, there has to be some trust.

Dependencies and supply chain attacks are a big thing - and yes, some people advocate dependency scanning and 
version pinning, but I don't think it is possible to use open source libs at scale without a certain amount of trust. 
I'm working in an organisation where there are hundreds of teams and thousands of microservices. I'm trying to think 
how we can assess the risk of thousands of dependencies and millions of lines of code. Without trust, the only way 
that's possible is to fork all libs, prevent open source and generally kill off any agility and velocity. 

My problem is that this weaponisation is killing off trust. I think the temptation of using open source projects as 
weapons against Russia should be resisted because it sets a dangerous precedent and may ultimately set back the open 
source movement and push organisation back into seeking refuge in commercial software with all it's opaqueness and 
obscurity.

It's not about sitting on the fence or taking sides in a war. It's about what open source has achieved over the last 
30 years and I think that's now at risk of become collateral damage.

Postscript:

This post was discussed on [Hacker News](https://news.ycombinator.com/item?id=30726098) and was republished by the
[IEEE Computer Society](https://www.computer.org/publications/tech-news/community-voices/on-the-weaponization-of-open-source).