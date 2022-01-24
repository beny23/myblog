---
title: "Exponential exploit: Why AppSec is hard"
date: 2021-02-28T01:50:10Z
tags: ["security", "java"]
---

In this blog post, I would like to explore how missing input validation even in a trivial service
can leave parts of server infrastructure crumbling. 

In my opinion, this why securing applications (AppSec) is very difficult.  Put supply chain attacks, unpatched systems 
and misconfiguring services to one side for a minute and consider that a lot of software is written by 
developers who do not necessarily consider all the edge cases or implications of what can go 
wrong even in the simplest of pieces of code (or just copy/paste from Stackoverflow).  
Unfortunately, vulnerable software in all sectors can get rushed out due to deadlines or inexperience 
and it is my opinion that poor coding practices in applications provide an interesting attack vector 
everywhere.

Allow me to demonstrate:  Given the following (admittedly oversimplified) problem statement:

> Write a microservice that increments a decimal number by one

Keeping it simple and very generic, I came up with the following Spring Boot application:

```java
@RestController
public class DemoController {

    @PostMapping("/increment")
    @ResponseBody
    DemoModel increment(@RequestBody DemoModel model) {
        DemoModel result = new DemoModel();
        result.setNumber(model.getNumber().add(BigDecimal.ONE));
        return result;
    }
}
```

My model looked like so

```java
public class DemoModel {
    private BigDecimal number;

    public BigDecimal getNumber() {
        return number;
    }

    public void setNumber(BigDecimal number) {
        this.number = number;
    }
}
```

A reasonable implementation.  I even used `BigDecimal` so that I don't have rounding/precision
issues, and the service is pretty trivial:

```bash
$ curl http://localhost:8080/increment \
  -d '{"number":1}' \
  -H "Content-Type: application/json"
{"number":2}
```

# What could go wrong?

As it turns out - quite a bit.

Here is a view of what the CPU did on my mac after hitting the service with a few requests over 
a 10-minute period:

![](/images/one_plus_one_is_crash_cpu_stats.png)

And here's the memory:

![](/images/one_plus_one_is_crash_memory_stats.png)

And what was the culprit. Not a vulnerability in Sprint Boot, not thousands of requests
(as a matter of fact there were just 4 requests, you can see it in the CPU graph, each time another request
came in, another CPU was used up 100%).

The requests were just:

```bash
curl http://localhost:8080/increment \
  -d '{"number":9999999e99999999}' \
  -H "Content-Type: application/json" >/dev/null
```

The number `9999999e99999999` has near enough `10,000,000` digits. If I wanted to send such
a number without the scientific notation (the `e` in the number), I'd have to send a payload that's
many 10 megabytes big.  That would get noticed very quickly.  However, the payload is miniscule. And when java 
is calculating this number (the plus 1 operation), it takes a long time because it tries to make an exact calculation.
I didn't wait around to find out how long the CPU was busy.  I just killed the
program after 10 minutes.

Imagine how much havoc such a vulnerability can create in a cloud setting, where instances sit in
Auto Scaling Groups.  Imagine 100 such requests hit your instances.  Every time we get 100% CPU, we 
spin up another instance, that's expensive.  And as the processing also uses up quite a bit of memory, 
it's very likely that other requests don't get a look-in.

This makes a very effective Denial of Service attack.  Sure, when identified it is easy enough to fix by
not allowing the scientific notation understood by `BigDecimal` as input to JSON, but that's not provided
as standard.  How can you be sure that your codebase isn't vulnerable?

In my opinion this is where development and testing teams need to have the experience in spotting these
potential issues.  The above is just one example of how a seemingly innocuous piece of code can be abused.

How would your code fare against the [Big List of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings)?

[Discuss on Hackernews](https://news.ycombinator.com/item?id=26297016)