---
layout: post
title: Root-me Realist ch4 writeup
tags: web pentesting
categories: Ctf-Writeups 
excerpt: Recently discovered this website called rootme.org (Link:) which has a lot of machines we can try for Pentesting newbies such as myself. I suggest everyone to check out the website once for learning purpose.
It features a standalone web application unlike VulnHub style of providing VM's to download and pentest. The realist section of this website features challenges which have a certain level of real vulnerabilities hidden behind a functional appearing application. The machine description also features resources from which one can make out that the challenge involves exploiting LFI (Local File Inclusion) in the website.
---
# Root-We writeup



## Description

Recently discovered this website called rootme.org (<a href="https://www.root-me.org/?page=news&lang=en">Link:</a>) which has a lot of machines we can try for Pentesting newbies such as myself. I suggest everyone to check out the website once for learning purpose.

It features a standalone web application unlike VulnHub style of providing VM's to download and pentest. The realist section of this website features challenges which have a certain level of real vulnerabilities hidden behind a functional appearing application. The machine description also features resources from which one can make out that the challenge involves exploiting LFI (Local File Inclusion) in the website.



## Initiation

The webpage is pretty uninteresting and not much to look around and fool around. The first thing we notice is that the website is written in PHP. The only page that accepts user-input is the login form. We see the URL at the top of the web page which looks extremely tempting to perform LFI against.
The URL image is shown below.



| ![rootwe-0.PNG]({{site.url}}/public/img/rootwe-1/rootwe-0.PNG) |
| :----------------------------------------------------------: |
|                      Injecting the URL                       |

However, even after trying with multiple payloads, the URL refuses to give us any sort of Local file inclusion or throw any  errors. Here we could be hitting one of two possibilities.

<ol><li>The web logic is suppressing PHP errors </li>
    <li>There is no LFI possible with the URL</li></ol>

The URL is also impervious to payloads of Directory Traversal attack. Since the URL seems to be not playing our game, we turn our attention to the login form or next obvious place where we could inject user input. Since LFI is the theme of this challenge we do not look for other attack vectors such as SQLi etc. After several payloads being injected into the username and password field of the login form, we do not get any results or exceptions.



##  *"All user-input is untrusted"*

In the scenario of web application security or application security in general , one thing is repeated over and over again - *All user input is untrusted*. Now what  else can the user inject? The user can not only inject a the payload of the post request (login form), but also controls the headers being sent. To do this we need a web proxy which will give us control over the http headers. 

My favourite tool for this purpose is Burp-Suite, but there are numerous ways to achieve this. Using burp we intercept the request from our browser. Make sure that you configure Burp suite as proxy. To do this just have your web browser point to the proxy at port 8080 (default for Burp Suite).

Once the burp suite is setup, intercept the post request submitted from the web login form. For trying out let our LFI payload be the straight forward *'/etc/passwd'*. We will replace this as the value in all user controlled inputs including the agent to zero in on any and all injection if possible as shown below.

| ![rootwe.PNG]({{site.url}}/public/img/rootwe-1/rootwe.PNG) |
| :--------------------------------------------------------: |
|           Injecting all headers with LFI payload           |



Once we do this, we find that we get a permissions error! Finally we have got our file injection to work and we just need to bypass a permission issue. After hit and trial we find that the user input we thank for the successful local file inclusion is the header *Accept-language*. We will target this header for our subsequent payloads to the login form.

| ![rootwe-2.PNG]({{site.url}}/public/img/rootwe-1/rootwe-2.PNG) |
| :----------------------------------------------------------: |
|              Successful LFI, but path inclusion              |

## Getting the inclusion to work

Since we face permission issue while trying to extract *'/etc/passwd'*, the web server might be running in non-root mode. We try then to include a file which the web server should have permissions on. The payload is now *'/var/log/apache*'*. We get  a file exists but the error being displayed is that the path we are trying to access is not within the allowed domains and configurations. The two options which came to my mind at this point was to do two things:

1. Get a RCE on the web server to change the permissions
2. Try to read the source code by getting the php  code in the current directory

The second option seemed more feasible, as this whole challenge was based around Web.  Hence while doing this I picked the second option.



## Fetching the PHP file

To fetch the file index.php we need a workaround as if included via the traditional LFI, it will end up getting rendered or compiled by the Apache webserver......unless we change its formatting  or encode it before including it, and here we will use the concept of PHP wrappers.

```php
php://filter/convert.base64-encode/resource=index.php
```

| ![rootwe-3.PNG]({{site.url}}/public/img/rootwe-1/rootwe-3.PNG) |
| :----------------------------------------------------------: |
|              Using the payload described above               |

Voila!!, we receive a base64 encoded version when injected into the *Accept-language* header. Once we do this we can decode it to get the php code of the file. We then notice it calls most of the variables from another file called **config.php**. We simply use the same technique to obtain **config.php** as well. 

| ![rootwe-3.PNG]({{site.url}}/public/img/rootwe-1/rootwe-4.PNG) |
| :----------------------------------------------------------: |
|                  PHP file in base64 format                   |

## Getting the password hash

On getting **config.php** , we notice that the web site uses the file-based sqlite called **db4realiste.sqlite** it is being included as a file. We retrieve the Sqlite file from the database using the same technique, but remember the decoding format from this file should be ASCII not UTF-8 unlike the PHP files.On decoding and reading it in any sqlite parser or use one available online. We then see the admin user and the md5 password hash being inserted into the database. Hence the only thing that is left is to crack it using a dictionary attack. We then obtain the password as **4dmin**.



| ![rootwepass.PNG]({{site.url}}/public/img/rootwe-1/rootwepass.PNG) |
| :----------------------------------------------------------: |
|              sqlite file reveals password hash               |



