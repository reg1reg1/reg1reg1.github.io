---
layout: article
key: 202002240
pageview: true
title: My first disclosure
tags: ctf
excerpt: My first ever public web security disclosure for a popular ecommerce website
---


## Overview	

On a sunday morning, I discovered that I had slept on my spectacles and broken them. In India , I always buy my spectacles from Lenskart, and was quite happy to discover that they deliver here in the US as well.	

While on the process of purchasing spectacles at Lenskart.com in their US site which is https://lenskart.us/ , I became curious at the URL being displayed for my order id.  To any person starting out in infosec, a .php extension in the URL always stands out glaringly, like a beetle among roaches.	

One thing led to another, and before long, I was using whatever was left of my spectacle-bereft-eyes to see how deep the rabbit hole went. Since I did not have prior permission, or consent from them I was cautious about not being too intrusive with my approach. I also had never disclosed a bug before and have little idea how bug bounties and disclosures work. 	



## The bug	

When I completed the transaction for my order at lenskart, I noticed that the orderURL in the browser showed something like this. Apart from the fact that it is bad web security practice to have mutliple subdomains have access to cookies, the orderId was in plain numbers in the URL.	

So it looked like this	

```	
https://{Vulnerable URL}/......?urlparamter={prefix}{num}	
```

I wanted to see whether I could change the num and be allowed to see a different order, and I could. Not only that I could also edit the order! If that was not enough, the order could be changed after it had been placed. Lenskart automates the spectacle process, and makes their own glasses based on prescriptions uploaded by the users or powers manually entered. I could change someone's order and have them fight lenskart for messing up their eyepowers.	

I could also download their prescription, and see their doctor's name, sometimes receipt and addresses, and to my surprise I found some clients from India (surprise because I believed that since they had different domains (.com India) and (.us USA) they would have different DC's. But then, they were using PHP with extensions too.) 	

The problems did not end there, the **orderId** field was being injected into the database query logic unfiltered or badly filtered and lead to full blown error messages when special characters were passed . The schema of the underlying SQL database was exposed as well, and more intrusive measures at this point to exploit would be unauthorized, and hence I did not go ahead with any of that. Also, I ran no automated tools or floods on the web application as I had no prior permission from Lenskart to run such scans.

## The process	

I had discovered this bug in mid December- with little information on how the disclosure works I came to know from what I read online is that the ethics of the process is that we notify the company first before making any kind of disclosure on public forums. I emailed Lenksart mentioning what I had found, attaching the screenshot, and asking them to fix the bug. 

Lenskart had multiple representatives reach out to me, and I had to explain to each one of them that what I had reported was not a customer grievance , and I was just a security enthusiast who had stumbled upon it. 	

I waited for about 2 weeks , before they replied that this issue has been fixed and the technical team has been alerted. It had not been fixed, and so I mailed them again . They asked me to be on a conference call with their developer team to describe the issue. At that time , I was having issues with my cellular network and getting my service transferred (Thanks AT&T!), and so I conveyed that I would prefer a Google hangouts call instead, or skype. 	

After this there was no communication between me and lenskart team. In february however, I decided to make a LinkedIn post about the exploit. Unfortunately, in my callousness I mentioned a bit too much information about the exploit. Many people were now exploiting this bug and hitting the website of lenskart.  

I was blisfully unaware, until I had someone from the security team reach out to me and explain me the situation over a zoom call. I took down the linkedin post and blog and apologized for the inconvenience caused. After this a prompt patch was put in place to prevent the bug from being exploited.



## Lesson from the experience

This being my first bounty brought with it some important insights- I learned that before exposing a bug or exploit , the meat of the exploit and the company name must be hidden before there is a confirmation from the other side. 

As a matter of fact, a lot of Indian companies do not have active bug bounty programs, and in lieu of that the disclosure and reward policies are not in place due to which an exposed bug could be exploited for damage.

