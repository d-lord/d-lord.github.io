---
layout: post
title:	"AusCERT Week in Review for Non-Techies"
date:	2017-12-21 23:10:00
published: true
excerpt: >
  Dad was complaining that the AusCERT Week in Review is hard to read, so I wrote a new version to be a little more user-friendly.
tags: infosec humour
thumbnail: assets/images/confused-cat.jpg
---

_Context: Dad was complaining that the [AusCERT Week in Review](https://www.auscert.org.au/blog/2017-12-22-auscert-week-review-22nd-december-2017) is hard to read. Don't take this too seriously. This writing reflects my personal opinions and not necessarily those of my employer._

Hi nerds,

2017 is almost over. Please renew your membership contracts whenever they come due.

AusCERT news:

The Call for Proposals is still open until January 19th for the AusCERT 2018 conference.

Someone published a lot of usernames and passwords. We told our members if their users were in them.

We’re going on holiday, don’t bug us. But if you really want to, check our contact page for the emergency hotline.

We’ve sponsored a conference.

This week in cybersecurity:

-------------------------------------------------------------------------------
**Unsecured Amazon S3 Bucket Exposes Details on 123 Million American Households**

[https://www.bleepingcomputer.com/news/security/unsecured-amazon-s3-bucket-exposes-details-on-123-million-american-households](https://www.bleepingcomputer.com/news/security/unsecured-amazon-s3-bucket-exposes-details-on-123-million-american-households)

Date: December 20 2017

Author: Catalin Cimpanu

Excerpt: More precisely, the database contained over 3.5 billion details for over 123 million American households.

The data included both personally identifiable information such as addresses, home details, contact information, or homeowner ethnicity, but also financial details such as mortgage status, financial histories, and purchase behavior.

*Simon version*: A contractor put a lot of information into “the cloud” so anyone could see. People saw.

-------------------------------------------------------------------------------
**Backdoor in Captcha Plugin Affects 300K WordPress Sites**

[https://www.wordfence.com/blog/2017/12/backdoor-captcha-plugin](https://www.wordfence.com/blog/2017/12/backdoor-captcha-plugin)

Date: December 19 2017

Author: Matt Barry

Excerpt: If you have not read our previous post on Mason Soiza, I’d suggest you read that first, since he has a long history of buying WordPress plugins in order to place cloaked backlinks on his users’ sites. He then uses these backlinks to increase page rank in SERPs (Search Engine Results Pages) since only web crawlers such as Googlebot can read them.

*Simon version*: Scumbag buys an existing program and makes it evil.

-------------------------------------------------------------------------------
**Fixing Data Breaches Part I: Education**

[https://www.troyhunt.com/fixing-data-breaches-part-1-education](https://www.troyhunt.com/fixing-data-breaches-part-1-education)

Date: December 18 2017

Author: Troy Hunt

Excerpt: You know the old "prevention is better than cure" idiom? Nowhere is it truer than with data breaches and it's the most logical place to start this series. The next 4 parts of "Fixing Data Breaches" are all about dealing with an incident once things go badly wrong, but let's start by trying to stop that from happening in the first place.

[Troy has published four articles so far of his five-part series, and they are worth reading.]

*Simon version*: Teaching people how not to lose data is the cheapest way of not losing data.

-------------------------------------------------------------------------------
**U.S. declares North Korea carried out massive WannaCry cyberattack**

[http://wapo.st/2yTFsPk](http://wapo.st/2yTFsPk)

Date: December 19 2017

Author: Ellen Nakashima & Philip Rucker

Excerpt: The Trump administration on Monday evening publicly acknowledged that North Korea was behind the WannaCry computer worm that affected more than 230,000 computers in more than 150 countries earlier this year.

*Simon version*: America reckons North Korea has been hacking people. _Someone_ did a lot of damage.

-------------------------------------------------------------------------------
And lastly, here are this week's most noteworthy security bulletins:

1\. **Chromium browser security update**

[https://www.auscert.org.au/bulletins/56290](https://www.auscert.org.au/bulletins/56290)

Chromium (and Chrome) 63.0.3239.108 address a flaw allowing a web page containing malicious content to cause Chromium to crash, execute arbitrary code, or disclose sensitive information when visited by the victim.

*Simon version*: If you visited a certain website in Chrome, then Chrome would be enslaved by bad guys.

2\. **otrs2 security update**

[https://www.auscert.org.au/bulletins/56198](https://www.auscert.org.au/bulletins/56198)

Two vulnerabilities were discovered in the Open Ticket Request System which could result in information disclosure or the execution of arbitrary shell commands by logged-in agents.

*Simon version*: Bad guys could get information from a helpdesk system, or could even enslave it.

3\. **Security vulnerabilities patched in VMWare products**

[https://www.auscert.org.au/bulletins/56322](https://www.auscert.org.au/bulletins/56322)

Successful exploitation of this issue could result in a low privileged user gaining root level privileges over the appliance base OS.

[note: multiple issues exist]

*Simon version*: A virtual machine is like a smaller computer you run inside your computer. It’s supposed to be more secure because bad guys can’t get out of the smaller computer into your main computer. Well, these ones could.

4\. **Apache vulnerability announced and patched in F5 Networks Products**

[https://www.auscert.org.au/bulletins/56386](https://www.auscert.org.au/bulletins/56386)

Apache modules `apache_auth_token_mod` and `mod_auth_f5_auth_token.cpp` allow possible unauthenticated bruteforce on the `em_server_ip` authorization parameter to obtain which SSL client certificates used for mutual authentication between BIG-IQ or Enterprise Manager (EM) and managed BIG-IP devices.

*Simon version*: The program which runs on a server so people can view your website and engage with your brand, could accidentally release information about how it talks to the program which protects it from bad guys. This doesn’t automatically let bad guys in, but it lets them sneak around and find holes.

-------------------------------------------------------------------------------

Merry Christmas, nerds.
