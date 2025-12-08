---
title: "Weakly Link 25/49"
date: 2025-12-07
tags: ["weakly-link"]
featured_image: "/images/weakly_link_2025_49_title.jpg"
---

This week, we're talking about React4shell. The latest in the 4shell family of vulnerabilities. What does
4shell mean? Usually that it's possible to do RCE (Remote Code Execution) based on an application vulnerability
and typically, attackers will leverage that to get a remote shell.

# And now, for something completely different

My first [link](https://hiddenlayer.com/innovation-hub/echogram-the-hidden-vulnerability-undermining-ai-guardrails/)
is not related to react4shell, but it's yet another way of how guardrails in LLM models can be 
subverted. Apparently adding `=coffee` to the end of a prompt is enough to confuse safety measures. 
I find that quite funny, for humans adding a caffeinated beverage tends to make us work better. Or in my
case in the mornings, makes me work. For AI guardrails, adding coffee, not so much. Mind you, it's nothing 
new. Spilling coffee over your laptop doesn't often end well for the machine.

# React4shell then

So t'interweb was all aflutter on the 3rd of December when 
[react announced a CVSS 10 vulnerability](https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components).
That write-up is quite nice. And I have to say when I first read it, I did think "oh, this could be big".
[Finding a POC](https://gist.github.com/maple3142/48bc9393f45e068cf8c90ab865c0f5f3) in the wild soon after
wasn't great either and I was wondering

> could this be the next log4shell?

Turns out I was not the only one and very soon the cybersecurity industry does what it does best and
mongers doom until everyone is jittery. Kevin Beaumont I think 
[put it best](https://doublepulsar.com/cybersecurity-industry-overreacts-to-react-vulnerability-starts-panic-burns-own-house-down-again-e85c10ad1607)

> The end isn’t nigh, the cloud isn’t falling, stop running off cliffs like Lemmings because 
> of warnings from the cybersecurity industry over this.

Talking of lemmings, one of the good reasons about having a Web Application Firewall (WAF) in front of
your application is generally a good idea, especially in situations like this. Is it a magic bullet? No,
not at all. Can it be circumvented? Probably most of the time. But it is a valuable part of a layered
defence. If there's a POC like this, the internet gets sprayed with scans that try to attempt the POC.
Log4shell was the same. It didn't take long before log files were filling up with `jndi`. Same is true here.
That's why it's nice to see that the WAF vendors react quite quickly. \[react quickly, geddit].

Cloudflare was [one of them](https://blog.cloudflare.com/waf-rules-react-vulnerability/) and promptly broke
quite a lot of the internet in the process. And it wasn't even DNS yet again. According to 
[this piece](https://blog.cloudflare.com/5-december-2025-outage/) it was another bit of configuration cockup
when Cloudflare rolled out bigger buffers to scan bigger payloads. Which React supports by default.
Ironically, the mitigations from the Cloudflare outage a couple of weeks ago should have mitigated
this issue too, but the fixes hadn't been rolled out yet.

# Yummy secrets with truffles

Coming to the end now, I was quite impressed by the simplicity of scanning a load of gitlab
repositories and finding secrets. [All it takes](https://trufflesecurity.com/blog/scanning-5-6-million-public-gitlab-repositories-for-secrets) 
is a bit of automation and the use of `trufflehog` to find secrets. You are running a secret scanner 
on your own repos, right? Right?

# It's Christmas

To finish off, this [privacy notice](https://www.royalmail.com/sites/royalmail.com/files/2024-11/Santa-Mail-Privacy-Notice-2024.pdf)
is very cute. Reminded me of Joel's take on 
[Santa's GDPR problems](https://joelgsamuel.medium.com/santa-claus-the-general-data-protection-regulation-gdpr-57f1571e7de8) 
a while back.

That was the weakly link, goodbye.