---
layout: article
pageview: true
titles:
  # @start locale config
  en      : &EN       Academic Work, Projects & Accomplishments
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  关于
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  關於
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  ko      : &KO       소개
  ko-KR   : *KO
  fr      : &KO       À propos
  fr-BE   : *FR
  fr-CA   : *FR
  fr-CH   : *FR
  fr-FR   : *FR
  fr-LU   : *FR
  # @end locale config

---

Elaborating below, some of the major areas that I have been working on in the last couple of years. I have also included any CTF's styled events that I have taken part in the last couple of years. I am still a year into infosec, and if you are someone who wants to work on something similar or different in technology/ infosec, shoot me an email or contact me at  **<a href="https://www.linkedin.com/in/yuvraj-singh-b29228a0/">LinkedIn</a>** or **<a href="https://github.com/reg1reg1">Github</a>** .

# 1. CyberMetrics

## *August 2019 - December 2019* under <a href="https://cybersecurity-strategy-masters.nyu.edu/faculty/edward-amoroso/">Professor Edward Amoroso </a>

Quantifying cyber risk is one of the biggest challenges in info sec today. Since the advent of the Orange book, people have been trying to quantify security or measure it in some form. Does the phrase "less" or "more secure" have any inference ? Though security may never be directly measured, can there be some metrics for it. There are various frameworks, and organizations such as PCI-DSS, NIST standards, BSIMM which focuses on software security.

This endeavour of cyber metrics done by the research team at NYU asks the question whether a reliable metrics of cyber metrics can be formed by asking the gut feeling of cybersecurity experts.

NYU's cyber metrics index of expert opinion or "gut feeling" on status of cyber security has been collecting opinion data over the last 8 years. This is an attempt to parse through and analyze the data with the ELK stack and conclude whether any corelations can be drawn between "gut feeling of experts" and "the actual state of cyber security".



A part of a team of 6 students in the Fall semester, working to find relation between various aspects of the data collected by survey started 8 years back by Dan Geer and Mukul Pareek. 

I worked on setting up the pipeline for pulling in the data, setting up a planned infrastructure for easier ingesting of data, and corelating the existing with various publicly available data related to cybersecurity breach, released CVE's to find patterns if any and aid in research work towards the technical paper on NYU's Cyber Index.

**<a href="https://github.com/reg1reg1/Metrics">Github Link to Project</a>**

![arch](assets/arch.png)

# 2. Implementing Network Security Monitoring

## *July 2019 - December 2019 under <a href="https://engineering.nyu.edu/faculty/thomas-reddington">Professor Thomas Reddington </a>*

Network security is a complex process involving a lot of different micro components that need to be fine tuned to orchestrate a smooth, fast , adaptive and scalable implementation. For an organization to have a hardened NSM model, it must go through thorough and rigourous designs, redesigns and reviews before arriving at an optimal implementation. The dynamic nature of the threat that a network faces adds to the challenge of the task at hand, and an organization must either outsource or have a dedicated team of professionals in this area who are continously trained to maintain the NSM and modify it when the need be. The Application Collection Framework and Collective Intelligence frameworks are some which provide good reference points in setting up NSM for a given network. From deciding sensor hardware and placement, to deciding algorithms that detect threats faster, to building a scalable architecture that can adapt to changes well- A network security analyst is expected to know a vast array of skills proficiently.

Technologies Used:

1. Justniffer, Zeek for PTSR data extraction
2. YAF, SiLK (Cert NetSA tools) for flow collection and generation
3. GNS3 For Network Simulation
4. Security Onion as a reference Sensor
5. Bash scripts for various tasks such as monitoring, logging, and miscellaneous other automations
6. Filebeat for shipping logs
7. FRR as router software
8. Application Collection Framework /Collection Intelligence Framework as guidance
9. Logstash for centrally collecting and transferring logs
10. Elasticsearch for distributed storage of logs.
11. Kibana for visual analysis of logs.
12. Ansible for managing multiple nodes in the network
13. Python for automation.

The full project can be found here: <a href="">Github Link </a>. I am still continuing work in this area, if you have similar interests would be happy to collaborate.
The whitepaper on the same can be found here:> <a href="https://github.com/reg1reg1/NSM-Implementation/blob/master/Project%20Whitepaper.pdf">Pdf link</a>

# 3. Academic Coursework: 

## Penetration Testing and Vulnerability Analysis: Paper Reviews

1. **Spectre and Meltdown**:  A scenario based MLA styled report which is written as a demonstrated communication between a security team of a company to the other teams and upper management explaining them the vulnerability Spectre and Meltdown and the impact to the organization, and measures that need to be taken. 
   View the full <a href="https://drive.google.com/file/d/1zhwDTxJjfm6K1X78l9bKbRbGD7m_ayUB/view?usp=sharing"> pdf report </a>. 
2. **MS08-067 Case study**: A scenario based MLA styled report written as a demonstrated communication between the security team of the company to other teams such as developer and upper management explaining the vulnerability to them and the impact towards the organization and measures that need to be taken.
   View the full <a href="https://drive.google.com/file/d/1y8UfV9bYe2MUOkUEL27D1ChWjHR2FFJa/view?usp=sharing"> pdf report </a>.
3. **Vehicle Hacking: Case study**: An MLA styled report explaining the vehicle hacking which was presented by Dr Charlie Miller and Chris Valasek on how a vehicle may be remotely hacked into. The security team explains the impact to the other teams of the hypothetical organization.
   View the full <a href="https://drive.google.com/file/d/1VtdAfIALLL11Yho7K0JnlljFBKdl8mpn/view?usp=sharing">pdf report</a>.

## Cyber Range Lab: Penetration Testing and Vulnerability analysis.

The final project of the course was to simulate a penetration testing scenario on company from breaking physical security to breaking into machines, and documenting the penetration testing work in a OSCP styled professional Pentesting report. It was a good window to the world of real world pentesting and a befitting final project to end the course. The full report can be <a href="https://drive.google.com/file/d/1jofO3kuWtXPCmnZE2KEUFiIgFA9NJvxd/view?usp=sharing">viewed here </a>:

