---
title: "Precision Munitions for Denial of Service"
date: 2023-01-30T23:48:32Z
tags: ["security","appsec","denial-of-service","playframework"]
featured_image: "/images/precision_munitions_for_denial_of_service_title.jpg"
---

There's a metaphor about the fight between attackers and defenders in the Denial of Service cybersecurity game. It's an 
"arms race" between [ever bigger attacks](https://www.cloudflare.com/en-gb/learning/ddos/famous-ddos-attacks/) throwing huge
amounts of traffic at ever more sophisticated defenses (e.g. [AWS shield](https://aws.amazon.com/shield/ddos-attack-protection/)).  

Incidentally, I've just demonstrated an easy mistake: I'm not describing a Denial of Service (DoS) attack, it's a Distributed Denial of 
Service (DDoS) attack. The aim is to overwhelm the infrastructure, either the networking infrastructure or the 
application by sending more requests than can be handled.

That's akin to carpet bombing:

- Drop lots of malicious requests on the target
- Hope something hits
- Can do it from great height
- Doesn't need much intelligence other than a rough location of the target

But that is not the only way to take out a target. Rather than carpet bombing, these days air strikes are carried out 
differently. Guided missiles use a lot less explosives but strike the intended target much more precisely.

# What would be a precision Denial of Service attack?

Recently, I've worked with a couple of security issues in the [Play Framework](https://github.com/playframework/playframework):

- [Denial of service when binding forms from JSON](https://github.com/playframework/playframework/security/advisories/GHSA-v8x6-59g4-5g3w)
- [StackOverflowError or NoClassDefFoundError is occurred when received form-data with many fields from multipart/form-data](https://github.com/playframework/playframework/issues/10590)

Both issues allowed attacks that used payloads that are less than 100 KB and introduced a `StackOverflow` error.

# Why is that important?

- By default, Play will try to parse 
    - `application/x-www-form-urlencoded`
    - `application/json`, 
    - `text/xml` and 
    - `multipart/form-data` payloads.
- By default, Play will [parse 100KB](https://www.playframework.com/documentation/2.8.x/ScalaBodyParsers#Max-content-length)
- By default, Play will [shut down](https://www.playframework.com/documentation/2.8.x/Shutdown) when encountering a 
  fatal error with an error message like this:

```
shutting down JVM since 'akka.jvm-exit-on-fatal-error' is enabled for ActorSystem[application]
```

The interesting bit here is that this happened while parsing the payload and caused the application to terminate 
before even reaching any code implemented by the service developers. These were bugs in the Play Framework. This meant that any
application could be shut down with just a generic payload and endpoints could be targeted with content types that
were not anticipated.

# Amplification

What was even more interesting was that the Play application was running inside a service mesh based on 
[Envoy](https://www.envoyproxy.io) and it was configured to automatically retry a request that resulted in a 
[HTTP 503 Service Unavailable](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/503).
Now normally, that's not a bad idea, because if a service is unavailable, then retrying it automatically means the
failure is dealt with without impacting the user. In this case however, when Play died it returned a 503, which then
got retried. So a single request could be used to kill 3 instances (because Envoy retried twice).

# But, but... it'll automatically restart

You might argue that with any platform if an instance dies, it automatically gets restarted.  With 
[Auto Scaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html) we can even ensure
that there are always enough instances.

Still, by using a simple shell script like this

```
seq 1 20 | \
  xargs -n1 -P10 -I{} \ 
  curl -s https://target.host/endpoint \
    -H "Content-Type: application/json" \ 
    -d @payload.json \ 
    -H "X-Iteration: {}" >/dev/null
```

20 requests can be sent in short order.  And if those 20 requests take down 60 instances, it is going to take a little while
for all of them to come back.

![Whack a mole](/images/precision_munitions_for_denial_of_service_whack_a_mole.png)

And then I can run the script again. And again. And again.

# WAF to the rescue! Or not.

The other interesting thing with this attack vector is that a Web Application Firewall (WAF) can be bypassed quite easily. 
As most WAFs only look at the first 8 or 16 KB and use regular expressions to find known bad patterns, it means that the 
payload can often be hidden by just prefixing it with 8K of whitespace.

And if the attacker keeps running 20 requests every minute, it is quite conceivable that they could keep a system
down for quite a while as it would be difficult to distinguish the attack payloads from normal payloads.

# What to do about it?

After discovering the bugs it was really easy to engage with the Playframework maintainers who fixed the issues and 
raised a CVE. The turnaround was quite impressive. Even more so when considering that Play is now supported by a
[community](https://opencollective.com/playframework). Upgrading Play is ultimately the best way to fix this.

# What other lessons are in here?

- The main takeaway for me is that frameworks and libraries try to be too helpful. I don't think it is a good idea
  for Play to automatically translate a JSON payload into something that can be used with a URL-encoded form request. 
  Or do XML processing when getting a `text/xml` payload regardless of whether the code was designed to handle it or
  not. I think code should be explicit, otherwise assumptions can soon leave us exposed: just look at the log4shell
  fiasco.
- Well meaning reliability features (such as the automatic Envoy retries) can sometimes cause more havoc than less - 
  note, I am not saying that Envoy's behaviour is wrong there!
- WAFs are an important defence mechanism but not the answer for everything
- To carry out precision strikes requires knowledge about the target system, but often it is easy to find out about
  who uses what framework.
- When evaluating vulnerabilities in frameworks, do not ignore CVEs with a lower CVSS score - 
  denial of service CVEs will not reach a score of (I think) higher than 7.5.  CVSS scores are a bit of a 
  [bugbear of of mine]({{< ref "curating_vulnerabilities.md" >}}) anyway!