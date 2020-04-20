---
layout: article
key: 202004101
pageview: true
title: Privacy by design - An Introduction
tags: blog
excerpt: Privacy has always been one of the areas of much debate and controversy. For a long time, it was debated whether the public and the people in general have the right to privacy. The government for the most part has always painted privacy as the tool crafted by the 4 horsemen for the vile. The dichotomies have not ceased to exist.
---

## Introduction and History: Privacy

Before going into the depth of privacy by design, we must understand what What even is privacy? Do we need privacy? And if yes, how do we define the degree of privacy or if there is something called absolute privacy. There have been 2 opposing sides when it comes to privacy as a right - The government, and other proponents warning about the harms and cons of anonymity and privacy being used by criminals and the others who believe in privacy as a right and the supporters of free software movement.

The wikipedia definition of privacy is __"**Privacy** is the ability of an individual or group to seclude themselves or information about themselves, and thereby express themselves selectively."__. Privacy has always been one of the areas of much debate and controversy. For a long time, it was debated whether the public and the people in general have the right to privacy. The government for the most part has always painted privacy as the tool crafted by the 4 horsemen for the vile. The dichotomies have not ceased to exist. In a simpler sense, it is giving the power of the owner of information about an individual or entity to share or distribute it at will. Ofcourse, there are caveats such as a suspect's house or computer might require searching, or count as forensic evidence, but there are procedures to that. Denying privacy means giving the government unwarranted right to search anyone , access all data without question, and without procedure.

The side arguing against the ill effects of privacy, primarily government and federal agencies, have painted privacy as the tool of 4 horsemen of apocalypse, a tool if unrestricted would prevent prosecution and conviction of criminals, a tool which would be exploited by enemies of the state - something they felt would far outweigh the pros. To an extent they made a point - You do not want those distributing child pornography , or a drug distributor to use this tool, or anonymity to evade law. However, the solution to strip everyone of privacy is surely not the solution. For a long time (and still), government influenced major advances in cryptography and privacy. There was not enough research being done, and government wanted backdoors or keys into encryption being made available to public. They wanted right to tap phone lines if they deem fit, and search anyone's online activity once they felt it "needs" to be done.  When Snowden leak happened in 2013, these were brought into more light. For a long time, in the post-world war era, cryptography remained a tool of the government and corporations. During this time, encryption required meeting of both parties to exchange keys, and hence there was little use in the public domain with little research being done in the area not government directed or initiated. 

In 1976, however, something revolutionary happened- researchers Diffie, Hellman and Merkle together solved the "key exchange" problem in cryptography birthing asymmetric encryption. Further research , would create the RSA and this would eventually power the SSL encryption in the Internet years later. The researchers working on it , faced backlash and pushback from the government, and did not just have to just solve the problem, but defend their "right to solve it". The paper called "New directions in Cryptography" introduced the "Diffie-Hellman" or "Diffie-Hellman-Merkle" key-exchange , in layman terms, allowed almost magically , two people to exchange keys over an insecure channel. As the famous Bruce Schneier implied that, "Imagine 2 people in a room full of mathematicians and cryptographers trying to listen and make sense of their conversation. These 2 people would appear to yell nonsense to the room, but at the end of it each of them would know a shared secret, and the mathematicians and cryptographers cannot figure it out, even though they listened to everything."

Once it became evident that encryption is going to be made available for general use, two sides defending and arguing against it would rise beginning the long debates and discussions in privacy. Debates, discussions are always important in such a landscape, and we have come a way from where the agencies could force anyone to give up their data, to a point where they now do it secretly. In practice, one may argue that nothing has changed, the agencies could do whatever they want, just be discreet about it. However, the very fact that the dialogue and pushback caused the users to be more aware of the digital footprints, and understand how the anonymity can be easily breached etc. More companies now aim to become GDPR compliant, and more users use VPN's when accessing the internet, and TOR project and DuckDuckGo provide a means to strengthen the notions of privacy online.

While I could rant on and on about this, I suggest you all to read on your own - There are some interesting books on this area, and if you would like to read up, these are 2 I recommend personally.

1. [ Crypto Rebels by Stephen Levy ][ https://www.amazon.com/Crypto-Rebels-Government-Privacy-Digital/dp/0140244328/ref=sr_1_fkmr0_1?dchild=1&keywords=crypto+wars+rebels+fought+back&qid=1587384129&sr=8-1-fkmr0]
2.  [Secrets & Lies by Bruce Schneier][https://www.amazon.com/Secrets-Lies-Digital-Security-Networked-ebook/dp/B004UARVS0/ref=sr_1_1?crid=1TS9XC6YC9ZRB&dchild=1&keywords=secrets+and+lies+bruce+schneier&qid=1587384584&sprefix=Secrets+and+lies+bruce%2Caps%2C148&sr=8-1]

So now,that we have an idea about privacy and how it came to be, let us discuss privacy by design.



## Privacy By Design: Introduction

We live in a digital world where we leave digital footprints. The services that make the digital and modern life sustainable ,  take in our information, our preferences, our search history etc. Our online identity and our offline identity can be linked , given enough data with a great degree of certainty, and most of us want to control the share and distribution of personal information. The organizations that claim to not misuse or share our data have to incorporate privacy policies and are expected to follow them.  The privacy by design is a framework based approach to designing systems and technologies with privacy in focus, and proposed by Ann Cavoukian.  Despite many criticism of being vague, and difficult to adopt, this laid the foundation which would later be adopted by the International Assembly of Privacy Commissioners and Data Protection Authorities in 2010. Theoretically the privacy by design has foundational 7 principles and you can read more in Ann's whitepaper on the topic "Privacy by design in Law". The 7 foundation principles as cited in the wiki of "Privacy by design" are as unders:

1. **Proactive instead of Reactive**:
2. **Privacy embedded into design**
3. **Full functionality - positive sum not zero sum**
4. **End-to-end security**
5. **Visibility and transparency**
6. **Respect for user privacy**
7. **Privacy as default**

When implementing, the target of the organization refer to these guidelines in their own manner to promote a better product/service design aligning with privacy guidelines , and become better custodians of use data. 



There are very few blacks and whites when it comes to privacy - There are  a lot of grey areas, and privacy implementations are not industry standards like PCI-DSS for security. There are no verifications for privacy by design implementations. There are many companies which have their own interpretations of the 7 foundational principles, and the companies adopt this in an effort to become GDPR compliant.







## Privacy By Design Implementation : An example

Let's say you are the privacy engineer working for a large organization which has little to no privacy embedded into design, and you handle or deal with a large amount of customer data. How do you go about strengthening the privacy back-bone of your organization?



### Step 1 : Identify data feeds and data storage:

The first step would be to identifying where the personal data or customer data is located in your organization - This is important, as only then would you be in a position to categorize it and associate the risks associated with that data.

The privacy team is responsible for collectively recognizing what part of customer data is PII (Personally Identifiable Information) say names, phone-numbers, ip's ,etc. This would vary from organization to organization, but once done, the team needs to then 







