---
title: "Cryptojacking: cryptography in service of the Devil"
date: 2018-01-07T12:33:21+08:00
categories: ["security"]
draft: false
---
A new  revenue stream has been implemented by the end of the last year. Maybe cryptojacking did not get yet the hype it deserves but it has been  already widely used. Most Internet consumers are not aware of it though its intentions are far from being innocent. It may also generate inequalities among Internet users.

## Definition
Cryptojacking is about using the user’s device processor, with or without his consent, to mine cryptocurrency when visiting a given webpage which is not necessarily compromised but can do that on purpose. It starts instantly upon visiting a cryptocurrency mining webpage. In its most nefarious form, cryptojacking does not require permission or any other form of effort from the user to be run. In other words, cryptojacking consists in hijacking the user’s devices (computer, smartphone, tablet ...etc) to pilfer digital profit. 
## Background
It all started on September 2017, when a company called Coinhive<sup>[1](#b1)</sup>  devised a mining component for the Monero (coin equivalent in Esperanto)  cryptocurrency that a webpage can hide in a form of a JavaScript script that looks as follows:
```javascript
<script src="https://coinhive.com/lib/coinhive.min.js"></script>
<script>
        var miner = new CoinHive.User('<site-key>', 'john-doe');
        miner.start();
</script>
```
The motivation behind Coinhive’s product was to offer an alternative  income stream for websites to ads which suffer from their own security pitfalls. This company offers a  flexible JavaScript API to play with the mining component at will. Hence   PirateBay is the first website used this API to raise funds<sup>[2](#b2)</sup>:<br/><br/>
*As you may have noticed we are testing a Monero javascript miner. <br/>
This is only a test. We really want to get rid of all the ads. But we also need enough money to keep the site running.<br/>
Let us know what you think in the comments. Do you want ads or do you want to give away a few of your CPU cycles every time you visit the site?*
<br/><br/>
Personally, I see such an approach equivalent to when you ask someone the permission to rape him. That said, I remember I read somewhere else that most PirateBay users accepted an in-browser mining to reduce adds. And since that, copycats have been cropping up to claim funds raising or reducing adds. Nefarious attackers can mine digital money for themselves by compromising a given website to take advantage of its traffic<sup>[3](#b3)</sup>. There is even a Google Chrome extension<sup>[4](#b4)</sup> which is removed from the Web Store for mining cryptocurrency in the background without the knowledge and consent of its users.
## POW CAPTCHA

To prevent spamming activities, website provide a challenge response test in the form of CAPTCHA to be solve by the visitor to check if that is a human or a machine.  But nowadays, most probably you have already encountered a website which relies on the proof of work CAPCHA. The concept of POW CAPTCHA<sup>[5](#b5)</sup>  consists in forcing your CPU to solve a mathematical puzzle in the form of a hashing algorithm for the Monero Blockchain. 

This is how POW CAPTCHA looks like:

![POW CAPTCHA](/pow_captcha.gif)

While this technique makes your surfing easier and flatters your laziness in that you do not need to solve the CAPTCHA manually by yourself, you pay the website by letting it exploit your CPU and raise your electrical bills. Of course, depending on the speed and power of your machine, you may wait more or less than other visitors to be able to surf on the website in question.

## Danger
This technique is more insidious than the most common malicious JavaScript attacks such as drive-by download ones which often lead to sneak nefarious software into the user’s machine. In practice, cryptojacking is mostly intentional and secretive.

Running several cryptojacked webpages may have dramatic consequences emanating from an overwhelmed machine’s processor: that can range from interrupted work to damaging the hardware (as it is the case with the infamous  Android trojan called Loapi<sup>[6](#b6)</sup>) passing by data loss or a glitch in an organization's network. There is a simpler yet effective impact to frequently land on cryptojacking webpages: that will increase your electric bills as the CPU of your machine is “forced” to work more.

## Prevention
Ad blockers can protect against cryptojacking. There is an extension called No Coin for the Google Chrome users. The last version of Opera (50)<sup>[7](#b7)</sup> ships with a built-in cryptojacking blocker. It is worth to mention that as soon as the user closes the tab of the cryptojacking webpage, the CPU cycles inherent to it and used for mining are freed (but this may not always the case.
Although lot of scanners already block Coinhive cryptojacking and categorize it as malware, attackers can always find a way to circumvent them (using obfuscated code, for instance) or by  hosting their own mining intermediary for JavaScript components to call back to.

## Note
POW CAPTCHA will prevent spamming activities because these later ones are done rather by computer botnet<sup>[8](#b8)</sup>, so the attacker does not worry about how much CPU power is taken from the  machines under his control. On the other hand, cryptojacking compromises the browsing user experience, and maybe one day everybody will have to buy a more expensive computer in order to surf on the Internet, or have to pay more electrical bills to read news online instead of paying for the conventional membership. Definitely, given our nature as humans, cryptojacking may be a source to nourish inequality among Internet consumers.

------
<sup><a name="b1"><sup>1</sup> </a>coinhive.com</sup><br/>
<sup><a name="b2"><sup>2</sup> </a>https://thepiratebay.org/blog</sup><br/>
<sup><a name="b3"><sup>3</sup> </a>https://www.washingtonpost.com/news/the-switch/wp/2017/10/13/hackers-have-turned-politifacts-website-into-a-trap-for-your-pc/</sup><br/>
<sup><a name="b4"><sup>4</sup> </a>https://gadgets.ndtv.com/apps/news/archive-poster-google-chrome-extension-cryptocurrency-mining-1794930</sup><br/>
<sup><a name="b5"><sup>5</sup> </a>https://www.cs.utexas.edu/~seang/blog/proof-of-work-captcha.html</sup><br/>
<sup><a name="b6"><sup>6</sup> </a>https://www.kaspersky.com/blog/loapi-trojan/20510/</sup><br/>
<sup><a name="b7"><sup>7</sup> </a>https://techcrunch.com/2018/01/03/opera-now-protects-you-from-cryptojacking-attacks/</sup><br/>
<sup><a name="b8"><sup>8</sup> </a>https://news.ycombinator.com/item?id=7944540</sup><br/>
