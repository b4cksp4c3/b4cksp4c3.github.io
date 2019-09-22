<center><h1>Haystack</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>

Start off with an Nmap scan

```
# Nmap 7.70 scan initiated Tue Jul  2 13:01:19 2019 as: nmap -sV -sC -T4 10.10.10.115
Nmap scan report for 10.10.10.115
Host is up (0.21s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 2a:8d:e2:92:8b:14:b6:3f:e4:2f:3a:47:43:23:8b:2b (RSA)
|   256 e7:5a:3a:97:8e:8e:72:87:69:a3:0d:d1:00:bc:1f:09 (ECDSA)
|_  256 01:d2:59:b2:66:0a:97:49:20:5f:1c:84:eb:81:ed:95 (ED25519)
80/tcp   open  http    nginx 1.12.2
|_http-server-header: nginx/1.12.2
|_http-title: Site doesn't have a title (text/html).
9200/tcp open  http    nginx 1.12.2
|_http-server-header: nginx/1.12.2
|_http-title: 502 Bad Gateway

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jul  2 13:01:49 2019 -- 1 IP address (1 host up) scanned in 30.16 seconds
```
A few ports open but it looks like there are two <b>http</b> ports. Navigating to <b>http://10.10.10.115</b> I am greeted with only a picture of a needle.

<center><img src="/htb/haystack/needle.png"></center>
<br>
If I download the image and look at it with <b>strings</b> I find a <b>base64</b> encoded string.
```
# wget http://10.10.10.115/needle.jpg
--2019-09-21 22:32:17--  http://10.10.10.115/needle.jpg
Connecting to 10.10.10.115:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 182982 (179K) [image/jpeg]
Saving to: ‘needle.jpg’

needle.jpg                                100%[===================================================================================>] 178.69K   709KB/s    in 0.3s    

2019-09-21 22:32:19 (709 KB/s) - ‘needle.jpg’ saved [182982/182982]
```
<b>Strings</b>
```
bGEgYWd1amEgZW4gZWwgcGFqYXIgZXMgImNsYXZlIg==
```
And if we <b>base64</b> decode that string we get a message.
```
# echo "bGEgYWd1amEgZW4gZWwgcGFqYXIgZXMgImNsYXZlIg==" | base64 -d
la aguja en el pajar es "clave"
```
The message translates to <b>The needles in the haystack is "key"</b>.

For now I can't do anything with that information so I now check out port 9200. First thing I see is some json data refering to <b>elasticsearch v6.4.2</b>
```
name	"iQEYHgS"
cluster_name	"elasticsearch"
cluster_uuid	"pjrX7V_gSFmJY-DxP4tCQg"
version
number	"6.4.2"
build_flavor	"default"
build_type	"rpm"
build_hash	"04711c2"
build_date	"2018-09-26T13:34:09.098244Z"
build_snapshot	false
lucene_version	"7.4.0"
minimum_wire_compatibility_version	"5.6.0"
minimum_index_compatibility_version	"5.0.0"
tagline	"You Know, for Search"
```
I have no idea what <b>elasticsearch</b> is so I do some reading on it. Turns out you can interact with it and dump all of the <b>json</b> data that is stored. To do this I am going to use *[this](https://github.com/taskrabbit/elasticsearch-dump)*
