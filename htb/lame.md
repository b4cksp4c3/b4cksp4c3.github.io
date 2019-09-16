# Lame

## Lame was a very easy box that really only required basic skill and the ability to google

<code>	
	Nmap 7.70 scan initiated Wed May 29 15:37:48 2019 as: nmap -sC -sV -T4 -oN LameNmap 10.10.10.3
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
</code>
