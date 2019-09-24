<center><h1>Ellingson</h1></center>
<br>
<center><h3>Summary</h3></center>

First start with an Nmap scan

```
# Nmap 7.70 scan initiated Sat Jun 15 12:15:32 2019 as: nmap -sV -sC -T4 10.10.10.139
Nmap scan report for 10.10.10.139
Host is up (0.042s latency).
Not shown: 998 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 49:e8:f1:2a:80:62:de:7e:02:40:a1:f4:30:d2:88:a6 (RSA)
|   256 c8:02:cf:a0:f2:d8:5d:4f:7d:c7:66:0b:4d:5d:0b:df (ECDSA)
|_  256 a5:a9:95:f5:4a:f4:ae:f8:b6:37:92:b8:9a:2a:b4:66 (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
| http-title: Ellingson Mineral Corp
|_Requested resource was http://10.10.10.139/index
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun 15 12:15:45 2019 -- 1 IP address (1 host up) scanned in 13.05 seconds
```
Only a couple ports open but I will be looking at port 80 specifically. Navigating to the web page I am greeted with a simple website.

<center><mg src="/htb/ellingson/home/png"></center>
<br>
Doing some manual enumeration reveals a post written by <b>The Plague</b> mentioning some of the most common passwords. If you dont recognize this quote it is from the movie <b>hackers</b>

<center><img src="/htb/ellingson/quote.png"></center>
<br>
