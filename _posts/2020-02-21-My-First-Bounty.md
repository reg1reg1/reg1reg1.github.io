---
layout: article
key: 20200221
pageview: true
title: My first public disclosure
tags: disclosure
excerpt: A simple broken spectacle led me down the rabbit hole of my first public bug bounty.
---

## Overview

On a sunday morning, I discovered that I had slept on my spectacles and broken them. In India , I always buy my spectacles from Lenskart, and was quite happy to discover that they deliver here in the US as well.

While on the process of purchasing spectacles at Lenskart.com in their US site which is https://lenskart.us/ , I became curious at the URL being displayed for my order id.  To any person starting out in infosec, a .php extension in the URL always stands out glaringly, like a beetle among roaches.

One thing led to another, and before long, I was using whatever was left of my spectacle-bereft-eyes to see how deep the rabbit hole went. Since I did not have prior permission, or consent from them I was cautious about not being too intrusive with my approach. I also had never disclosed a bug before and have little idea how bug bounties and disclosures work. 


## The process

I had discovered this bug in mid December- with little information on how the disclosure works I came to know from what I read online is that the ethics of the process is that we notify the company first before making any kind of disclosure on public forums. I emailed Lenksart mentioning what I had found, attaching the screenshot, and asking them to fix the bug. 

Lenskart had multiple representatives reach out to me, and I had to explain to each one of them that what I had reported was not a customer grievance , and I was just a security enthusiast who had stumbled upon it. 

I waited for 15 days , before they replied that this issue has been fixed and the technical team has been alerted. It had not been fixed, and so I mailed them again . They asked me to be on a conference call with their developer team to describe the issue. At that time , I was having issues with my cellular network and getting my service transferred (Thanks AT&T New York!), and so I conveyed that I would prefer a Google hangouts call instead, or skype. 

After this I got ghosted till January mid, where I decided to check the website again. It seemed that they had put something which looked like a fix, but it was just like hiding the trash under the rug, and the real flaw still persisted and very much exploitable. 

I wrote an email to them that since a month had passed from the time I notified them, I would take the process upto hackerone, where most of the technical details of this are posted. In the last week of January , a hackerone moderator notified them and I did not hear back still till now, and I felt that now I could make a responsible public disclosure.

## The bug

When I completed the transaction for my order at lenskart, I noticed that the orderURL in the browser showed something like this. Apart from the fact that it is bad web security practice to have mutliple subdomains have access to cookies, the orderId was in plain numbers in the URL.

So it looked like this

```
https://app.lenskart.sg/......?orderId={prefix}{num}
```

I wanted to see whether I could change the num and be allowed to see a different order, and I could. Not only that I could also edit the order! If that was not enough, the order could be changed after it had been placed. Lenskart automates the spectacle process, and makes their own glasses based on prescriptions uploaded by the users or powers manually entered. I could change someone's order and have them fight lenskart for messing up their eyepowers.

I could also download their prescription, and see their doctor's name, sometimes receipt and addresses, and to my surprise I found some clients from India (surprise because I believed that since they had different domains (.com India) and (.us USA) they would have different DC's. But then, they were using PHP with extensions too.) 

The problems did not end there, the orderId field was being injected into the database query logic unfiltered or badly filtered and lead to full blown error messages when special characters were passed . Since I am new, I did not want to get into legal problems and have my spectacle sue me and rob me blind (pun intended), I stopped there.



## 'Fix by Lenskart'

They only ended up removing the URL from being exposed on the client side, and hashed it to prevent someone from stumbing upon it. We all know how security with obscurity is a bad idea, and all the errors still persists.

## Status

The lenskart team has not replied back, and I am waiting on status from hackerone(Lenskart is external).

### URL

https://**app.lenskart.sg**/myprescription-inter.php?orderno=orderINTR1959

## Screenshots



| ![sql.PNG]({{site.url}}/public/img/lenskartblowup.PNG) |
| :----------------------------------------------------------: |
|                      Injecting the orderID                   |





 
