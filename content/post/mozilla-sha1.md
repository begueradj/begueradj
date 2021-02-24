---
title: "Mozilla beheads SHA-1"
date: 2017-01-17T12:33:21+08:00
categories: ["security"]
draft: false
---
You have heard of SHA-1 deprecation since 2012. Mozilla finally confirms this is not a joke.

May be you are tired of hearing over and over again about SHA-1 deprecation since 2012 when Bruce Schneier [announced](https://www.schneier.com/blog/archives/2012/10/when_will_we_se.html) that collision attacks may be cost around $700k to perform by 2015, which was the year where the first practical full-on collision attack was lead  out by [researchers](https://sites.google.com/site/itstheshappening/) who predicted a costless real world attack may occur on 2018. 

Since that, Google, Microsoft and browsers manufacturers have raced the migration from SHA-1 to SHA-2. On last Tuesday, Mozilla gave the pace for this rush by being the first to display the warning *Untrusted Connection* on its Firefox browser to users visiting a website which SSL/TLS certificate is signed by SHA-1 instead of SHA-256.

Now you may think this paranoia makes no sens as no real world attacks exploiting this technique are known by today. My answer is that lack of paranoia is a synonym of unconsciousness: publicly, there are no known attacks, that is true, but under the hoods who knows? Also, history teaches us how MD5 which is more prone to collision attacks was equivalently used in cyber espionnage attacks against Iran through the famous [Flame](https://en.wikipedia.org/wiki/Flame_(malware)) malware.

The SSL/TLS certificate installed for my website uses SHA-2 as this Python script says:
```python
import os
os.system('openssl s_client -connect www.begueradj.com:443 < /dev/null 2>/dev/null\
          | openssl x509 -text -in /dev/stdin | grep "Signature Algorithm"')
```
Output:
```python
Signature Algorithm: sha256WithRSAEncryption
Signature Algorithm: sha256WithRSAEncryption

```

I just wonder how much this migration would cost as, in real life, SHA-1 underpins around [35%](https://www.venafi.com/blog/deprecation-denial-why-are-35-of-websites-still-using-sha-1) of the digital certificates existing today resulting without mentioning the nightmares engineers are enduring regarding their unsupported applications or hardware  reconfigurations to perform.
