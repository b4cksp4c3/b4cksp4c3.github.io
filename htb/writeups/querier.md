<center><h1>Querier</h1></center>
<br><br>
First we start with an nmap scan

```
# Nmap 7.80 scan initiated Sun Oct 13 16:10:37 2019 as: nmap -sV -sC -T4 -p- 10.10.10.125
Nmap scan report for 10.10.10.125
Host is up (0.059s latency).
Not shown: 65521 closed ports
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info:
|   Target_Name: HTB
|   NetBIOS_Domain_Name: HTB
|   NetBIOS_Computer_Name: QUERIER
|   DNS_Domain_Name: HTB.LOCAL
|   DNS_Computer_Name: QUERIER.HTB.LOCAL
|   DNS_Tree_Name: HTB.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2019-10-13T20:11:31
|_Not valid after:  2049-10-13T20:11:31
|_ssl-date: 2019-10-13T20:18:54+00:00; +58s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 57s, deviation: 0s, median: 57s
| ms-sql-info:
|   10.10.10.125:1433:
|     Version:
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2019-10-13T20:18:48
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct 13 16:17:56 2019 -- 1 IP address (1 host up) scanned in 438.55 seconds
```
So there are a lot of ports open but most of them are completely useless. Lets takes a look at the ```smb``` shares first. I'll use ```smbmap``` to list the shares.
```
# smbmap -u none -p none -H 10.10.10.125
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.10.10.125...
[+] IP: 10.10.10.125:445	Name: 10.10.10.125                                      
	Disk                                                  	Permissions
	----                                                  	-----------
	ADMIN$                                            	NO ACCESS
	C$                                                	NO ACCESS
	IPC$                                              	READ ONLY
	Reports                                           	READ ONLY
```
Looks like there is one shared folder named ```Reports``` that we have Read only permission to. Next I will mount the drive so I can take a closer look.
```
# mount -t cifs //10.10.10.125/Reports /mnt -o rw
```
Now if I navigate to the ```/mnt``` directory and list all the files in there, I will see one file.
```
# cd /mnt
# ls
'Currency Volume Report.xlsm'
```
When opening the file a message pops up telling us that the file contains macros. This gives us a hint of where to look.

<center><img src="/htb/querier/macros.png"></center>
<br>
