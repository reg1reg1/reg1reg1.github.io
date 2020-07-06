---
layout: article
key: 202006291
pageview: true
title: Exploring pfSense Firewall (2/2)
tags: blog network
excerpt: pfSense is not just a firewall, it has several inbuilt package support and functionalities such acting as a router, acting as a loadbalancer, and also package support for Squid Proxy. In this blog, I will go over some of these functionalities of pfSense and how to implement and test them.
---

# Pf-sense Firewall, Part-2



## Overview 

In my last blog on pfSense, I covered how to try out some of the basic firewall functionality of the FreeBSD-based firewall. However pfSense is more than just a firewall, it can act as a router and support functionalities such as those of a loadbalancer and a failover. It even has package support for SquidProxy web cache, and VPN technologies such as IPSec and OpenVPN. In this blog, I will show you how to go about playing with some of these features of pfSense on your own.

The functionalities that I will cover in this blog are :

1. Configuring pfSense as an internal load balancer.
2. Configuring pfSense as a SquidProxy web Cache
3. Configuring IPSec (site-site) tunnel mode in pfSense.



## Prerequisites

It is expected that you have the prerequisites mentioned in the last blog. Also, you should have an installed copy of pfSense on FreeBSD. Netgate offers a direct pre-pfSense installed version of FreeBSD OS. This blog will go over some of the concepts of IPSec, but not too deep in the conceptual parts. I will post some links at the end for you guys to get a hang of IPSec. Also, a basic understanding of networking concepts such as routing and gateways will come handy to understand some parts covered in this blog.



## Section:1 pfSense: As a load balancer

pfSense can act as a load balancer for your webservers. The setup involves setting up the pfSense before the webservers on a subnet and loadbalancing them. The Setup shown in GNS3 in the image below involves the following assets. The purpose here is to balance or schedule incoming web requests or inbound traffic to one of the web servers in a round-robin fashion.



1. GNS3 VM or GNS3 running on server
2. Web server or containers for web servers. Here I used a container I created for another project. If you want, you may use the same container image which runs a web server on port 5000. Note however, that session persistence is not managed by the pfSense loadbalancer, and has to be done externally.
3. pfSense on FreeBSD and one machine for accessing and administering the pfSense using the web GUI.


| ![failover.PNG]({{site.url}}/public/img/pfsense/failover.PNG) |
| :----------------------------------------------------------: |
|          Image-1:*pfSense GNS3 loadbalancer setup*           |



#### Setup and Implementation instructions

1. Make sure pfSense is configured to be reachable via the Admin machine / Web GUI is accessible.
2. Configure the containers for GNS3 or the web servers to be on the same subnet. The "em3" interface connected to the subnet containing the webservers could be configured to use a DHCP. In this case however, it is preferable to have them statically configured as the IP addresses will need to be entered into the load balancer pool. 
3. Check if you can ping the "em3" interface of the pfSense from the webserver machine, or ensure in any other way that you have network connectivity to each of the web servers/containers.
4. In this scenario we have 3 web server containers. We have IP addresses 10.0.0.a, 10.0.0.b, 10.0.0.c for each of the web server containers. The em3 has the ip address in the same subnet at 10.0.0.1. 
5. Click on **Services->Load Balancer->Pools** from the Web-sense GUI. Add the webserver Ip's to the pool as shown below.

| ![failover.PNG]({{site.url}}/public/img/pfsense/loadvirt.PNG) |
| :----------------------------------------------------------: |
|          Image-2:*Create the virtual server pool*           |

6. Once this is done we need to configure the load balancer and mention the port it will listen to. The load balancer will be listening on this port for incoming request to the webservers, and load balance them among the Ip addresses mentioned in the pool above. This is shown below, and done from the menu **Services->Load Balancer->Virtual Servers** .

| ![failover.PNG]({{site.url}}/public/img/pfsense/virtserver.PNG) |
| :----------------------------------------------------------: |
|          Image-3:*Add the server pool to the load balancer*  |

7. Once all is done, save the changes. To see if the webservers are up and running, hit the IP address first for each webserver at
10.0.0.a:5000/10.0.0.b:5000/10.0.0.c:5000. If they work, try hitting the load balancer. The load balancer will forward and select a web server in round robin fashion. Hit the load balancer at 10.0.0.1:8080, where 8080 is the port you configured at the virtual server menu above.





## Section 2: pfSense as Squid Proxy Server and Web cache

pfSense can act as a web cache and transparent proxy to reduce bandwidth usage on outbound internet traffic with package support of Squid Proxy. SquidProxy is a web cache based on the harvest cache daemon and has been around since the 90's. A lot of firewalls configure SquidProxy for caching web requests and also restricting access to certain domains, keeping track of viruses , and network layer restriction on internet access etc.

You would need a OS with browser and access to the internet intercepted by the pfSense , and an admin machine (optional) for administering the pfSense. You could use 1 machine for both the purposes as well. 

### Setup and Implementation instructions

1. We need pfSense setup with any one or more of the Lan interfaces. If you are creating an additional interface for testing the proxy, make sure you allow the traffic in like specified in Lan (em1) rules by default. For this setup I used a separate interface and subnet outside the default Admin Lan interface for testing out pfSense as shown below.


| ![squid.PNG]({{site.url}}/public/img/pfsense/squidProxy.PNG) |
| :----------------------------------------------------------: |
|          Image-3:*Add the server pool to the Squid Proxy*  |

2. Before you go ahead, install the squid proxy package into the pfSense. This can be done from the WebGUI. Select **System->Package Manager->Available Packages**. You can search for squid proxy package from the search bar and then install it.
3. Once installed, it should show up in the installed pacakges.
4. Once it is done, we can configure it from **Services->Squid Proxy Server**. Make sure you enable the server, and add the interfaces on which the proxy server will enabled as shown in the image below.

| ![squid.PNG]({{site.url}}/public/img/pfsense/proxySettings.PNG) |
| :----------------------------------------------------------: |
|          Image-4:*squid proxy settings and server*  |

5. If you plan to enable SSL filtering, make sure you generate your own CA certificate from pfSense and install it in the browser so that the SSL certificate is properly authorized by pfSense.
6. For system optimization such as cache size, in memory cache size and RAM size, and object size for in memory caching, go to the **Local Cache ** tab.
7. You can enable ClamAv and Safe Google browsing for Squid Proxy in **Local Cache**.
8. The other important settings comes under the tab **ACL's** where you can allow different subnets, and block and allow different domains. Note that if you block domains with **"https"** , you need to enable SSL filtering and install your self-signed certificates in browsers.







