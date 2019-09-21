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
