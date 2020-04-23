---
layout: article
key: 202004221
pageview: true
title: Introduction to Differential Privacy
tags: blog
excerpt: The digital services we use are extensively data driven. As the digital age advances, our reliance on technology is exponentially increasing. Devices like Alexa listen to what we speak, our locations are used for services like Uber and lyft, and we leave more digital footprints than ever before. This age requires a new definition of privacy where data driven services and analytics can co-exist with privacy - Differential Privacy is the answer.
---

## Introduction: Differential Privacy

Privacy preserving data analysis . 50+ year old problem. Census : Needs data for well being of people, but preserve pricacy? How to solve this problem?

De-Identify data? another term for de anonymization.

Learn same things if an individual is removed from the data set. Did we compromise privacy? A definition of choice. What if a martian finds out that all humans have 2 feet? Note that if a person is removed, we would still learn the same thing. We learnt about humans not the individual. 

But can learning about population hurt an individual's privacy?  

strike balance between data privacy and providing functionality.  Companies need data from customers to improve on their products but the customers need privacy.

Data anonymization is hard. Simply removing names does not help. 

In 2006, Netflix tried to do this by creating Netflix prize. It was a competition for finding algorithm which could predict user ratings based on an existing dataset. To protect privacy they randomized names, and added some noise to the data , some random ratings which seems solid.

2 researchers Texas University, linkage attack , de anonymizing large sparse dataset research paper.

Govt massachusett. mid 1990s . co related. public data. Zipcode birthday and gender could identify most individuals.

Overly accurate statistics can blatantly compromise privacy. 



Mathematical definition of diff privacy.

Algorithm A gives (epsilon) diff privacy if for all pairs of data sets x,y differing in the data of 1 entity/person and all events S.  If for all pairs x,y

Pr[A(x)∈S] = q . Pr[A(y) ∈ S]

S here is the set of all possible outcomes of the queries forwarded by adversary to the Oracle or the data storage and the above equation says that the Algorithm A run on the dataset x to return the Outcome 's' and the same algorithm run on dataset y to return the outcome for 's' would differ by a very small quantity q. If q is small, then Algorithm A succeeds in this context for differential privacy.



Concept of plausible deniability. Randomly flipping data and answer. 

Laplace distribution paper. Algorithmic Foundation of differential Privacy.

Apple and google use differential privacy. Documented but not open source. Chrome on browser malware, trafic in cities 

Not many companies have adapted diff priv. Only usable for large datasets because of  introduced noise and small datasets would be simply rendered incorrect.

Can data be collected for utility without compromising privacy ? Diff priv attempts to ans that



Four facets of differential Privacy.



--- Part 3 -----



NYC taxi data was also compromised. Re-identification attack. Anonymized data that can be linked to identifying data to recover the anonymized information . Netflix example. NYC taxi data , celebrity tips was recovered. Publishing anonymized data? How do you know what could be out there that could link to your data set or 10 years into the future someone publishes another anonymized data set which could be linked 

Differential privacy is not a procedure, but a definition of privacy. A new definition of privacy. What does it mean to have differential Privacy?

<Insert diagram here>

The mathematical definition of differential privacy. No ill to the data contributor is the goal of differential privacy. Epsilon is the measure of the privacy that we get. Diff privacy guarantees this? Why so less usage? Lot of theoretical work, however these are specialized and general purpose implementations are hard, and hence rare.

What would constitute a real-world implementation of differential privacy?

- Have broad support for analytic queries
- Usability for non-experts
- Easy integration with existing architecture and data environments.



Different mechanisms for differential privacy

- Laplace Mechanism
- PINQ
- Elastic sensitivity
- Sample and Aggregate
- Restricted sensitivity

Easy integration with existing data environments. Must support multible database engines. Exisiting systems might need data 





UBER: Chorus framework. Usable by non-experts. Analyst does not need to understand diff priv. Has brod supp for analyt queries. Modular design to support wide variety of mechanisms. 
Designed for real-world use.



What makes Chorus so good?

- Rewrites SQL queries, while earlier approaches modify the database mechanisms, chorus does not require any such changes to the database engine or data. This satisfies easy integration with existing data environments.
- What it does is modifies the SQL queries in place to convert them to intrinsically private queries. It is a query rewriting system.
- Query has embedded diff privacy, it can be run on any database, and these would generate differentially private results and this would be returned to the analyst.

**Example Mechanism: Elastic Sensitivity (Developed at UC berkley)**

Sensitive query result. Add noise. differentially privacy result. noise is drawn from the laplace distribution. Amount of noise depends and scaled to queries' sensitivity. Now the next question is how do we decide the sensitivity of a query? This is determined by the approach termed elastic sensitivity. Some queries are more sensitive to information of individual.

Elastic sensitivity: Efficiently computed upper bound on query sensitivity. THE novel part is it supports joins.



Rewriter for elastic sensitivity. 



