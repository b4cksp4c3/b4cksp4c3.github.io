<center><h1>Bastion</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Access an open SMB share to find a full Windows backup file
- Grab the SAM hases and crack the password
- mRemoteNG is install and the password is stored inside the configuration file
- Use credentials to SSH as Administrator
<br><br>
First we start with an Nmap scan

```
# Nmap 7.70 scan initiated Wed May 22 14:41:53 2019: nmap -sV -sC -T4 10.10.10.134
Nmap scan report for 10.10.10.134
Host is up (0.038s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
22/tcp  open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)
| ssh-hostkey:
|   2048 3a:56:ae:75:3c:78:0e:c8:56:4d:cb:1c:22:bf:45:8a (RSA)
|   256 cc:2e:56:ab:19:97:d5:bb:03:fb:82:cd:63:da:68:01 (ECDSA)
|_  256 93:5f:5d:aa:ca:9f:53:e7:f2:82:e6:64:a8:a3:a0:18 (ED25519)
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -46m34s, deviation: 1h09m15s, median: -6m36s
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-05-22T20:35:28+02:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2019-05-22 14:35:27
|_  start_date: 2019-05-19 22:23:54

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 22 14:42:11 2019 -- 1 IP address (1 host up) scanned in 17.97 seconds
```
<br>
