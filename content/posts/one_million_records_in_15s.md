---
title: "One million records in 15 seconds"
date: 2020-05-30T10:29:17+01:00
category: ["performance"]
---

![](/images/one_million_records_in_15s_title.jpg)

In this  post, I'd like to talk about optimisations that I recently used to provide a Scala Microservice that 
surfaced payment events. The events were held in an Oracle backend and the Microservice was in a docker 
container with (1 vCPU) allocated 512MB to the container and 256MB to the JVM that was running it. 
In this writing I'm not going to talk about the Oracle optimisations to make the underlying query fly but 
rather would like to concentrate on the kinds of things that can make service code quick.

# First Iteration

I've implemented my fair share of microservices that surface a SQL Query as JSON during my time. Using Scala is making 
this rather simple.

I would have a repository class dealing with JDBC - in this instance we're using Stored Proc, so Slick or 
Anorm were of limited use here - hence straight JDBC:

```scala
val cs = connection.prepareCall("call BULK_GET_EVENTS(?, ?)")
cs.setLong("P_LAST_EVENT_NO", lastEventNo)
cs.setLong("P_MAX_EVENTS", maxEvents)
cs.registerOutParameter("P_EVENTS", OracleTypes.CURSOR)
cs.executeUpdate()

val rs = cs.getObject("P_EVENTS").asInstanceOf[ResultSet]
new Iterator[PaymentEvent] {
  def hasNext(): Boolean = rs.next  def next(): PaymentEvent = PaymentEvent(
    eventNo = rs.getLong("EVENT_NO"),
    paymentStatus = rs.getString("PAYMT_STATUS"),
    statusChangeReason = Option(rs.getString("STATUS_CHG_RSN")),
    statusChanged = rs.getTimestamp("STATUS_CHG_DATE").toInstant)
}.toList
```

And there would be a controller

```scala
def findEvents(lastEventNo: Long, maxEvents: Option[Long]) = Action.async { implicit request =>
  tokenAuthenticated {
    repository.findEvents(lastEventNo, maxEvents) map (result => 
       Ok(Json.toJson(PaymentEvents(result))))
  }
}
```

Simple enough, but not very effective. When pushing the service to surface 10s of thousands of records, it struggled.

![Service struggling to cope](/images/one_million_records_in_15s_struggling.png)

Spending seconds in the garbage collector sure was a clear indication that we were running out of memory.

# Optimisation: Use Streaming

My hunch was that creating a big `List[PaymentEvent]` all in memory and then transforming it all into JSON 
in one go wasn't very good. It would be so much better for memory and performance if I could "stream" the 
response. I.e. start sending JSON responses down the wire before the DB query had even finished. Fortunately, 
Play/Scala is rather good at it - if only a little less staightforward.

First off, we change the code that accesses the stored proc:

```scala
val rs = cs.getObject("P_EVENTS").asInstanceOf[ResultSet]
val iter = new Iterator[PaymentEvent] {
  def hasNext() = rs.next
  def next() = PaymentEvent(
    eventNo = rs.getLong("EVENT_NO"),
    [..]
    statusChanged = rs.getTimestamp("STATUS_CHGDATE").toInstant)
}.toSeq 
```

The main change is very subtle. `.toList` was changed to `.toSeq` - but there is a world of difference. When calling 
`.toList` on an Iterator, all the elements of the iterator are turned into a list, whereas `.toSeq` is lazy - which 
means that the iterator is worked as and when elements in the sequence are needed. This means that after the method 
returns, it hasn't actually iterated over the JDBC ResultSet yet. Furthermore, the following controller changes all 
the results to be "streamed" - (Play is built on Akka Streams)

```scala
repository.findEvents(lastEventNo, maxEvents) map {
  case (result, callback) =>
    val source = Source.fromIterator(() => result.iterator.map(r => ByteString(Json.stringify(Json.toJson(r)))))
      .intersperse(ByteString("""{"events":["""), ByteString(","), ByteString("]}"))
      .alsoTo(Sink.onComplete( => callback.onComplete()))

    Ok.sendEntity(HttpEntity.Streamed(source, None, Some("application/json")))
}
```

Instead of transforming a whole List into JSON using `Json.toJson`, here we are sending "HTTP chunks" as responses, 
which means that we can start sending the response BEFORE we've finished building it. 
Stepping through the code: 

1. `Source.fromIterator(() => ...)` will use the sequence and iterate across it and create a stream of PaymentEvent objects 
1. `result.iterator.map(r => ByteString(Json.stringify(Json.toJson(r)))` turns an individual PaymentEvent object 
(i.e. a DB row) into a binary representation of a JSON string 
1. `intersperse(...)` will create the array event that PaymentEvents did in the original code (we're returning an array) 
1. `alsoTo(Sink.onComplete(_ => callback.onComplete()))` will be called when the streaming finishes to close the 
JDBC connection Note, none of those steps actually happen until `Ok.sendEntity(HttpEntity.Streamed(source...))` gets 
called at which point Akka takes over and "materialises" the stream as it is sent down the wire. 
During my investigation this worked rather well. I pushed the service to return half a million records in 14 
seconds with curl measuring the output:

```text
100 74.8M    0 74.8M    0     0  5412k      0 --:--:--  0:00:14 --:--:-- 5634k
```

Not bad! But we could do better ## Optimisation: Bigger Fetch Size I was able to optimise the query further 
(500,000 records in 8 seconds) by tweaking the JDBC connection settings

```hocon
hikaricp {
  [...]
  dataSource {
    defaultRowPrefetch = 1000
  }
}
```

By default the Oracle driver fetches 10 results at a time - which will result in 50,000 network roundtrips - by changing 
the `defaultRowPrefetch` property on the connection, it would fetch 1,000 results at a time, reducing the number of 
network roundtrips.

# Optimisation: Iterator instead of Seq

I wanted to to push the service again, and found that when I asked it to load 1 million records - it fell over

```text
java.lang.OutOfMemoryError: GC overhead limit exceeded
    at akka.dispatch.ForkJoinExecutorConfigurator$AkkaForkJoinPool.execute(:33)
[..]
    at akka.dispatch.ExecutorServiceDelegate$class.execute(:217)
```

So something wasn't streaming!

I fired up "jvisualvm" and did a heap dump locally and found

![Seq holding on to references](/images/one_million_records_in_15s_jvisualvm.png)

What was happening here was that the "lazy" Seq wasn't as lazy, but hanging on to an initial reference. Further 
research gave me the following: https://pedrorijo.com/blog/scala-streams/

So I replaced the toSeq with using an Iterator directly:

And then I retried to run my query for 1 million records - and while it was quick, it wasn't working properly. 
Half the records were missing.

It turns out that using the iterator like I had was a problem:

```scala
val iter = new Iterator[PaymentEvent] {
  def hasNext() = rs.next
  def next() = PaymentEvent(
    eventNo = rs.getLong("EVENT_NO"),
    [..]
  statusChanged = rs.getTimestamp("STATUS_CHG_DATE").toInstant)
}.toSeq
```

The contract for Iterator states that `hasNext` should not have any side effects. And my initial implementation 
DID have side effects. It moved the JDBC ResultSet to the next record. But if I called `hasNext` twice (which is 
exactly what happens when Akka streams the response), I'm skipping records.

So the final task was to create an iterator that would fulfil that non-side-effect contract:

```scala
val iter = new Iterator[PaymentEvent] {
  private var available = rs.next()

  def hasNext() = available 
  def next() = {
    val event = PaymentEvent(
      eventNo = rs.getLong("EVENT_NO"),
      [..]
      statusChanged = rs.getTimestamp("STATUS_CHG_DATE").toInstant)

    available = rs.next()
    event
  }
}
```

Running the performance test 1 million records arrived back safely:

```text
100  149M    0  149M    0     0  9953k      0 --:--:--  0:00:15 --:--:-- 10.1M
```

and the memory stats? Hardly touched:

![](/images/one_million_records_in_15s_stats_1.png)
![](/images/one_million_records_in_15s_stats_2.png)
![](/images/one_million_records_in_15s_stats_3.png)

# Conclusion

When dealing with big responses (or requests for that matter) - it does not necessarily need lots and lots of RAM! 
Using the right approach (streaming results and setting sensible fetch sizes) can make a big difference.
