<center><h1>Wall</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Use <b>curl</b> to perform a <b>POST</b> request to reveal a hidden directory
- Bruteforce the login credentials using <b>bash</b> and a <b>python</b> script
- Use the web interface to execute commands on the System to gain a reverse shell
- Exploit <b>GNU Screen 4.5.0</b> to get a root shell
<br><br>
First start with an Nmap Scan

```
# Nmap 7.80 scan initiated Fri Sep 20 15:45:55 2019 as: nmap -sV -sC -p- 10.10.10.157
Nmap scan report for 10.10.10.157
Host is up (0.059s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 2e:93:41:04:23:ed:30:50:8d:0d:58:23:de:7f:2c:15 (RSA)
|   256 4f:d5:d3:29:40:52:9e:62:58:36:11:06:72:85:1b:df (ECDSA)
|_  256 21:64:d0:c0:ff:1a:b4:29:0b:49:e1:11:81:b6:73:66 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Sep 20 15:46:43 2019 -- 1 IP address (1 host up) scanned in 47.73 seconds
```
Only 2 ports open. Lets start with port 80. Navigating to <b>http://10.10.10.157</b> we are greeted with an apache default installation

<center><img src="/htb/wall/home.png"></center>
<br>
Next I performed a <b>gobuster</b>. It only spit out a few files/directories
```
# gobuster dir -u http://10.10.10.157 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -t 30
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.157
[+] Threads:        30
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php
[+] Timeout:        10s
===============================================================
2019/09/21 18:48:24 Starting gobuster
===============================================================
/aa.php (Status: 200)
/monitoring (Status: 401)
/panel.php (Status: 200)
/server-status (Status: 403)
```
Checking out each result yields not much that can help me. The only thing that really I thought was promising was <b>monitoring</b> since it prompts us for a Username and Password to log in with. I tried guessing the usual credentials but had no luck so I moved on.

<center><img src="/htb/wall/monitoring.png"></center>
<br>
Next I did a basic <b>Nikto</b> scan to see what it would pick up. Nothing amazing was found but it did tell me which <b>HTTP Methods</b> are allowed
```
# nikto -h http://10.10.10.157
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.10.157
+ Target Hostname:    10.10.10.157
+ Target Port:        80
+ Start Time:         2019-09-21 18:52:22 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Server may leak inodes via ETags, header found with file /, inode: 2aa6, size: 58cb1080cb0d2, mtime: gzip
+ Apache/2.4.29 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: OPTIONS, HEAD, GET, POST
+ 7863 requests: 0 error(s) and 6 item(s) reported on remote host
+ End Time:           2019-09-21 19:00:56 (GMT-4) (514 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
Using this info I used <b>curl</b> to send requests to each result from <b>gobuster</b>, Eventually I found what I was looking for.
```
# curl -X POST http://10.10.10.157/monitoring/
<h1>This page is not ready yet !</h1>
<h2>We should redirect you to the required page !</h2>

<meta http-equiv="refresh" content="0; URL='/centreon'" />
```
Sending a <b>POST</b> request to <b>http://10.10.10.157/monitoring/</b> reveals another directory <b>/centreon</b>.
