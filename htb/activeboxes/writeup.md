<center><h1>Writeup</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>

<br><br>
First start with an Nmap scan
```
# Nmap 7.70 scan initiated Sat Jun  8 15:42:31 2019 as: nmap -sV -sC -T4 10.10.10.138
Nmap scan report for 10.10.10.138
Host is up (0.17s latency).
Not shown: 998 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey:
|   2048 dd:53:10:70:0b:d0:47:0a:e2:7e:4a:b6:42:98:23:c7 (RSA)
|   256 37:2e:14:68:ae:b9:c2:34:2b:6e:d9:92:bc:bf:bd:28 (ECDSA)
|_  256 93:ea:a8:40:42:c1:a8:33:85:b3:56:00:62:1c:a0:ab (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
| http-robots.txt: 1 disallowed entry
|_/writeup/
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Nothing here yet.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun  8 15:43:20 2019 -- 1 IP address (1 host up) scanned in 49.10 seconds
```
Only a couple ports open. I can already see that port 80 has a directory <b>/writeup</b>. Navigating to the page we see a very basic website that does not give us much to work from.

<center><img src="/htb/writeup/web.png"></center>
<br>
If you take a look at the web page source code then you should see that the website is running <b> CMS Made Simple</b>.

<center><img src="/htb/writeup/cms.png"></center>
<br>
