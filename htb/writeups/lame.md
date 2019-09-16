<center><h1>Lame</h1></center>

<center> Lame was a simple box that could have been easily completed with just basic linux knowledge and the ability to use google</center>

<br>
irst we start with an nmap scan. Here I did a scan of only the most common ports but it is good practice to scan all ports just to be safe

```
# Nmap 7.70 scan initiated Wed May 29 15:37:48 2019 as: nmap -sC -sV -T4 -oN LameNmap 10.10.10.3
Nmap scan report for 10.10.10.3
Host is up (0.39s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
	ftp-anon: Anonymous FTP login allowed (FTP code 230)
 	ftp-syst:
   	STAT:
	FTP server status:
      	Connected to 10.10.14.4
      	Logged in as ftp
      	TYPE: ASCII
      	No session bandwidth limit
      	Session timeout in seconds is 300
      	Control connection is plain text
      	Data connections will be plain text
      	vsFTPd 2.3.4 - secure, fast, stable
	End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
	ssh-hostkey:
  	1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
  	2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
	Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
	clock-skew: mean: -22h12m19s, deviation: 0s, median: -22h12m19s
 	smb-os-discovery:
   	OS: Unix (Samba 3.0.20-Debian)
   	NetBIOS computer name:
   	Workgroup: WORKGROUP\x00
  	System time: 2019-05-28T13:26:15-04:00
	smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done at Wed May 29 15:39:14 2019 -- 1 IP address (1 host up) scanned in 85.91 seconds
```
<br>
So there are a few ports open. The first thing that I noticed was the anonymous login for FTP. Upon quick inspection I realized there was nothing there. Next port that caught my eye was 445 running SMB. We can see that it is running version 3.0.20 and with a quick google search we find this vulnerability

<center> <img src="/htb/lame/exploit.png"> </center>
<br>
Now I start up Metasploit and load the script located at <b>exploit/multi/samba/usermap_script</b>. I set the <b>RHOSTS</b> to my IP and then type <b>exploit</b>. If everything goes well then you should see something like I do below.

<center><img src="/htb/lame/metasploit.png"></center>
<br>
So I got a shell. Typing <b>whoami</b> will reveal that I am root.

<center><img src="/htb/lame/root.png"></center>

The user flag is stored in <b>/home/makis/user.txt</b> and the root flag is located at <b>/root/root.txt</b>
<br><br><br>
