<center><h1>Forest</h1></center>
<br><br>
<center><h2>Summary></h2></center>
<br>
Start with our nmap scan

```
# Nmap 7.80 scan initiated Thu Oct 24 02:34:24 2019 as: nmap -vv --reason -Pn -sV -sC --version-all 10.10.10.161
Nmap scan report for forest.htb (10.10.10.161)
Host is up, received user-set (0.11s latency).
Scanned at 2019-10-24 02:34:24 EDT for 619s
Not shown: 989 closed ports
Reason: 989 resets
PORT     STATE SERVICE      REASON          VERSION
53/tcp   open  domain?      syn-ack ttl 127
88/tcp   open  kerberos-sec syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2019-10-24 02:42:27Z)
135/tcp  open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn  syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open  ldap         syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds syn-ack ttl 127 Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp  open  kpasswd5?    syn-ack ttl 127
593/tcp  open  ncacn_http   syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped   syn-ack ttl 127
3268/tcp open  ldap         syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped   syn-ack ttl 127
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1h32m08s, deviation: 4h02m29s, median: -3h52m09s
| p2p-conficker:
|   Checking for Conficker.C or higher...
|   Check 1 (port 4288/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 32753/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 32421/udp): CLEAN (Timeout)
|   Check 4 (port 44587/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: FOREST
|   NetBIOS computer name: FOREST\x00
|   Domain name: htb.local
|   Forest name: htb.local
|   FQDN: FOREST.htb.local
|_  System time: 2019-10-23T19:50:20-07:00
| smb-security-mode:
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb2-security-mode:
|   2.02:
|_    Message signing enabled and required
| smb2-time:
|   date: 2019-10-24T02:50:22
|_  start_date: 2019-10-24T00:50:23

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Oct 24 02:44:43 2019 -- 1 IP address (1 host up) scanned in 618.45 seconds
```
From this I can determine the host is a Winders Server 2016 with Active Directory. I can get a list of users using ```rpcclient```.
```
# rpcclient -U "" -N 10.10.10.161
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[DefaultAccount] rid:[0x1f7]
....<output omitted>....
user:[sebastien] rid:[0x479]
user:[lucinda] rid:[0x47a]
user:[svc-alfresco] rid:[0x47b]
user:[andy] rid:[0x47e]
user:[mark] rid:[0x47f]
user:[santi] rid:[0x480]
```
