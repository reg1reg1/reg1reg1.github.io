---
layout: article
key: 20200221
pageview: true
title: Starting in Incident Response and Network Security
tags: blog
excerpt: A resource guide to those starting out in Incident Response  and Network Forensics
---


## Overview

When I joined the MS in Cybersecurity program at NYU, my aim was to learn more and more about Networks and Cryptography- the thrust which had driven me to pursure further studies.  Both of these are huge domains , and vastly different and branch out and support a lot of other core skills in security like  Penetration testing, Blue Teaming, Red Teaming, Blockchains, Cryptocurrency etc.

I hope this blog serves as a guide to people who want to chalk out a plan to know more about Network Forensics and Incident Response. It is a basic guide to get started in Incident Response and once this is done, there are several other advanced skills which one can learn to advance further.



## What is Incident Response?

In the context of Networks, an "Incident" or "event" is an occurrence that needs special attention of the Network administrator or the managing and administering body.  What is and isn't an incident is not hardlined - It varies from organization, situation, context and what are the assets and what are the risk assosciated with them.

Broadly speaking, Incident response is identifying and reacting to threats in the network in real or near-real time. The main pillars of Incident Response are Collection,Detection and Analysis. A large part of it is understanding how to efficiently implement the three phases, the requirements and the compromises involved and how to make them co-exist together with the available hardware resources and technical expertise available.



## Pre-requisite skills and resources

### Computer Networks

It is fair to say that before you go about securing something, you need to understand how it works and operates. Also, it is much easier to defend and monitor a well-designed network. It is as important to understand the theoretical concepts as well as it is to implement and practice them in real life. 

The courses which helped me are-

1. **David Bombal's CCNA course**: Some of it is available on Coursera. He is a pioneer in teaching concepts of Networks clearly and keeping things simple and practical. You can find his course and ton others at gns3 academy.
2. **Lazaro Diaz** CCNA: An absolute gold, and teaches with an over the top enthusiasm that will keep you awake like caffeine injected into your brain. Teaches a lot of hands-on stuff. You can find his online course on multiple platforms.
3. **Other Tools/Skills:** It is also worthwhile to acquire these skills which help along the way. **Wireshark**, **GNS3 (Network Simulator)**, **Python** (Really helpful as has libraries like **Scapy**)



### Linux/Firewalls

You will find yourself configuring interfaces, and configurations a lot, and it is always helpful to be familiar with the Linux Shell. There are lot of books in Linux, but I found the following to be really helpful. Another important thing is the concept of **Linux Firewalls** which is the foundation stepping stone for learning how to configuring them .

1. Linux Firewalls :https://www.amazon.com/Linux-Firewalls-Detection-Response-iptables/dp/1593271417
2. Linux  Networking Cookbook: https://www.amazon.com/Linux-Networking-Cookbook-Easy-Use/dp/0596102488



### Network Security

For learning network security the best place to start would be with these courses



2. Udemy's course on Network security (Part of a course) is good: https://www.udemy.com/course/network-security-course/?ranMID=39197&ranEAID=a1LgFw09t88&ranSiteID=a1LgFw09t88-7pkZrnOC4Geg0_gshtAi9A&LSNPUBID=a1LgFw09t88
2. Vivek Ramachandran's course on network security would also help out a great deal. The other courses offered by him are pure gold too.

### Analytics/Data Analysis Stack:

Incident response involves a lot of parsing through data and identifying the patterns that are eventful, and thus involves a lot of data cleaning and analysis. During my implementation into incident response, I realized how important data analysis is to the process.
One should learn a basic data anaysis tool stack , but my personal favourite is the **ELK Stack (Elasticsearch/Kibana** as they offer a 1-stop easy to learn and scale solution. The other favourites are **Splunk**.

### IDS tool

Configuring and using an IDS tool will be a crux to Incident Response and the SIEM process, and there are plenty of options available. My personal and absolute favorite is **Zeek**, as it is transparent and highly customizable. **Snort ** is a good place to start as well.

### Automation tool

With large number of machines, and systems to manage, you would require knowledge of automating actions over machines, patch management and deploying solutions/softwares to a lot of remote machines at once. Tools like **Ansible**, and **Puppet** will come handy in this stage of the process.

### Generic Handbook/Study Guide

****

There are books which serve as great reference guide for the process, and as a handbook. My personal favorites are -

1. **Applied Network Security Monitoring**: https://www.amazon.com/Applied-Network-Security-Monitoring-Collection/dp/0124172083 
2. **Network Security Assessment:** https://www.amazon.com/Network-Security-Assessment-Know-Your/dp/149191095X/



I personally worked on this as an Advanced Project in my third semester of graduate studies.

Github Repo for the work done:> https://github.com/reg1reg1/NSM-Implementation 

  

