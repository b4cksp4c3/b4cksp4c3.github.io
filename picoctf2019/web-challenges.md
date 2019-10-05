<center><h1>Web</h1></center>
<br>
<center><h1>Insp3ct0r [50]</h1></center>
<br>
<h2>Question</h2>Kishor Balan tipped us off that the following code may need inspection: https://2019shell1.picoctf.com/problem/11196/ (*[link](https://2019shell1.picoctf.com/problem/11196/)*) or http://2019shell1.picoctf.com:11196

<h2>Answer</h2>You can find the flag in 3 parts by looking at the source code.  
```
<!doctype html>
<html>
  <head>
    <title>My First Website :)</title>
    <link href="https://fonts.googleapis.com/css?family=Open+Sans|Roboto" rel="stylesheet">
    <link rel="stylesheet" type="text/css" href="mycss.css">
    <script type="application/javascript" src="myjs.js"></script>
  </head>

  <body>
    <div class="container">
      <header>
	<h1>Inspect Me</h1>
      </header>

      <button class="tablink" onclick="openTab('tabintro', this, '#222')" id="defaultOpen">What</button>
      <button class="tablink" onclick="openTab('tababout', this, '#222')">How</button>

      <div id="tabintro" class="tabcontent">
	<h3>What</h3>
	<p>I made a website</p>
      </div>

      <div id="tababout" class="tabcontent">
	<h3>How</h3>
	<p>I used these to make this site: <br/>
	  HTML <br/>
	  CSS <br/>
	  JS (JavaScript)
	</p>
	<!-- Html is neat. Anyways have 1/3 of the flag: picoCTF{tru3_d3 -->
      </div>

    </div>

  </body>
</html>
```
The first part is at the bottom of the page, ```picoCTF{tru3_d3```, the second part is in the ```mycss.css``` function at the bottom, ```t3ct1ve_0r_ju5t```. Last part is in the ```myjs.js``` function at the bottom, ```_lucky?9df7e69a}```.

Flag:```picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?1ce1e9f5}```
<br>
<center><h1>dont-use-client-side [100]</h1></center>
<br>
<h2>Question</h2>Can you break into this super secure portal? https://2019shell1.picoctf.com/problem/32259/ (*[link](https://2019shell1.picoctf.com/problem/32259/)*) or http://2019shell1.picoctf.com:32259

<h2>Answer</h2>Once again inspect the web page source code.
```
<html>
<head>
<title>Secure Login Portal</title>
</head>
<body bgcolor=blue>
<!-- standard MD5 implementation -->
<script type="text/javascript" src="md5.js"></script>

<script type="text/javascript">
  function verify() {
    checkpass = document.getElementById("pass").value;
    split = 4;
1.    if (checkpass.substring(0, split) == 'pico') {
7.      if (checkpass.substring(split*6, split*7) == 'b956') {
2.        if (checkpass.substring(split, split*2) == 'CTF{') {
5.         if (checkpass.substring(split*4, split*5) == 'ts_p') {
4.          if (checkpass.substring(split*3, split*4) == 'lien') {
6.            if (checkpass.substring(split*5, split*6) == 'lz_e') {
3.              if (checkpass.substring(split*2, split*3) == 'no_c') {
8.                if (checkpass.substring(split*7, split*8) == 'b}') {
                  alert("Password Verified")
                  }
                }
              }

            }
          }
        }
      }
    }
    else {
      alert("Incorrect password");
    }

  }
</script>
<div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:yellow">
<div style="text-align:center">
<p>This is the secure login portal</p>
<p>Enter valid credentials to proceed</p>
<form action="index.html" method="post">
<input type="password" id="pass" size="8" />
<br/>
<input type="submit" value="verify" onclick="verify(); return false;" />
</form>
</div>
</div>
</body>
</html>
```
There are a bunch of if statements with checkpass functions each containing part of the password that I labeled 1 through 8 that can reconstruct the flag.

Flag:```picoCTF{no_clients_plz_eb956b}```
<br>
<center><h1>logon [100]</h1></center>
<br>
<h2>Question</h2>The factory is hiding things from all of its users. Can you login as logon and find what they've been looking at? https://2019shell1.picoctf.com/problem/21895/ (*[link](https://2019shell1.picoctf.com/problem/21895/)*) or http://2019shell1.picoctf.com:21895

<h2>Answer</h2>To get this flag we have to log in as admin. The problem is according to the hint the login doesnt check the password. If we try to login as admin with any password it logs in successfully but doesn't output the flag.

<center><img src="/picoctf2019/images/admin.png"></center>
<br>
From here we can use the Cookie Editor firefox extension to edit the admin cookie. By changing the value from False to True its like telling the server we successfully logged in with the correct password

<center><img src="/picoctf2019/images/cookie.png"></center>
<br>
Now refresh the page and you should get the flag

<center><img src="/picoctf2019/images/logonflag.png"></center>
Flag:```picoCTF{th3_c0nsp1r4cy_l1v3s_3294afa0}```
<br>
<center><h1>where are the robots [100]</h1></center>
<br>
<h2>Question</h2>Can you find the robots? https://2019shell1.picoctf.com/problem/12267/ (*[link](https://2019shell1.picoctf.com/problem/12267/)*) or http://2019shell1.picoctf.com:12267

<h2>Answer</h2>If you know anything about web development then you should know about the ```robots.txt``` file. The name of the problem and link in the problem both hint to the file as well. So visiting the page we see it mentions another web page

<center><img src="/picoctf2019/images/robots.png"></center>
<br>
Navigating to the ```/713d3.html``` web page reveals the flag

<center><img src="/picoctf2019/images/robotsflag.png"></center>
Flag:```picoCTF{ca1cu1at1ng_Mach1n3s_713d3}```
<br>
<center><h1>Client-side-again [200]</h1></center>
<br>
<h2>Question</h2>Can you break into this super secure portal? https://2019shell1.picoctf.com/problem/21886/ (*[link](https://2019shell1.picoctf.com/problem/21886/)*) or http://2019shell1.picoctf.com:21886

<h2>Answer</h2>We can construct the flag by looking at the source code of the web page again
```
<html>
   <head>
      <title>Secure Login Portal V2.0</title>
   </head>
   <body background="barbed_wire.jpeg" >

      <script type="text/javascript" src="md5.js"></script>
      <script type="text/javascript">
         var _0x5a46=['9f266}','_again_1','this','Password Verified','Incorrect password','getElementById','value','substring','picoCTF{','not_this'];(function(_0x4bd822,_0x2bd6f7){var _0xb4bdb3=function(_0x1d68f6){while(--_0x1d68f6){_0x4bd822['push'](_0x4bd822['shift']());}};_0xb4bdb3(++_0x2bd6f7);}(_0x5a46,0x1b3));var _0x4b5b=function(_0x2d8f05,_0x4b81bb){_0x2d8f05=_0x2d8f05-0x0;var _0x4d74cb=_0x5a46[_0x2d8f05];return _0x4d74cb;};function verify(){checkpass=document[_0x4b5b('0x0')]('pass')[_0x4b5b('0x1')];split=0x4;if(checkpass[_0x4b5b('0x2')](0x0,split*0x2)==_0x4b5b('0x3')){if(checkpass[_0x4b5b('0x2')](0x7,0x9)=='{n'){if(checkpass[_0x4b5b('0x2')](split*0x2,split*0x2*0x2)==_0x4b5b('0x4')){if(checkpass[_0x4b5b('0x2')](0x3,0x6)=='oCT'){if(checkpass[_0x4b5b('0x2')](split*0x3*0x2,split*0x4*0x2)==_0x4b5b('0x5')){if(checkpass['substring'](0x6,0xb)=='F{not'){if(checkpass[_0x4b5b('0x2')](split*0x2*0x2,split*0x3*0x2)==_0x4b5b('0x6')){if(checkpass[_0x4b5b('0x2')](0xc,0x10)==_0x4b5b('0x7')){alert(_0x4b5b('0x8'));}}}}}}}}else{alert(_0x4b5b('0x9'));}}
      </script>
      <div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:gray">
         <div style="text-align:center">
            <p>New and Improved Login</p>
            <p>Enter valid credentials to proceed</p>
            <form action="index.html" method="post">
               <input type="password" id="pass" size="8" />
               <br/>
               <input type="submit" value="verify" onclick="verify(); return false;" />
            </form>
         </div>
      </div>
   </body>
</html>
```
If you look specifically at the this javascript ```var _0x5a46=['9f266}','_again_1','this','Password Verified','Incorrect password','getElementById','value','substring','picoCTF{','not_this'];``` we can reverse engineer the flag.

Flag:```picoCTF{not_this_again_19f266}```
<br>
<center><h1>Open-to-admins [200]</h1></center>
<br>
<h2>Question</h2>This secure website allows users to access the flag only if they are admin and if the time is exactly 1400. https://2019shell1.picoctf.com/problem/49858/ (*[link](https://2019shell1.picoctf.com/problem/49858/)*) or http://2019shell1.picoctf.com:49858

<h2>Answer</h2>Just need to create two Cookies. One to make it look like we are Admin and the other to make it look like the time is exactly 1400.

```Admin Cookie```
<center><img src="/picoctf2019/images/opentoadmincookie.png"></center>
<br>
```Time Cookie```
<center><img src="/picoctf2019/images/opentoadmintime.png"></center>
<br>
Refreshing the page will gives us the flag

<center><img src="/picoctf2019/images/opentoadminflag.png"></center>
Flag:```picoCTF{0p3n_t0_adm1n5_effb525e}```
<br>
<center><h1>picobrowser [200]</h1></center>
<br>
<h2>Question</h2>This website can be rendered only by picobrowser, go and catch the flag! https://2019shell1.picoctf.com/problem/45071/ (*[link](https://2019shell1.picoctf.com/problem/45071/)*) or http://2019shell1.picoctf.com:45071

<h2>Answer</h2>This can be done by intercepting the request using burpsuite. First let's navigate to the web page and see what happens if we try to grab the flag. It gives us an error message.

<center><img src="/picoctf2019/images/nopicobrowser.png"></center>
<br>
Now if we open burp, start our proxy, and turn intercept on, and then click the "Flag" button again. You should see a request similar to this.

```
GET /flag HTTP/1.1
Host: 2019shell1.picoctf.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://2019shell1.picoctf.com/problem/45071/flag
Cookie: ga=GA1.2.2039358133.1567560624
Connection: close
Upgrade-Insecure-Requests: 1
```
Forward this request until you see one like this. The difference is the ```GET``` part of the second request is to a different file.
```
GET /problem/45071/flag HTTP/1.1
Host: 2019shell1.picoctf.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://2019shell1.picoctf.com/problem/45071/flag
Cookie: ga=GA1.2.2039358133.1567560624
Connection: close
Upgrade-Insecure-Requests: 1
```
Now lastly, edit the ```User-Agent:``` to say ```picobrowser``` and then forward the request. This should give you the flag.

<center><img src="/picoctf2019/images/picobrowserflag.png"></center>
<br>
Flag:```picoCTF{p1c0_s3cr3t_ag3nt_b3785d03}```
<br>
<center><h1>Irish-Name-Repo 1 [300]</h1></center>
<br>
<h2>Question</h2>There is a website running at https://2019shell1.picoctf.com/problem/27383/ (*[link](https://2019shell1.picoctf.com/problem/27383/)*) or http://2019shell1.picoctf.com:27383. Do you think you can log us in? Try to see if you can login!

<h2>Answer</h2>Navigating around the web page we will find a login page. Doing a simple SQL injection by entering ```admin' or '1'='1``` as the username and anything as the password will reveal the flag

<center><img src="/picoctf2019/images/irish1login.png"></center>
<br>
<center><img src="/picoctf2019/images/irish1flag.png"></center>
<br>
Flag:```picoCTF{s0m3_SQL_1fe77718}```
<br><br>
<center><h1>More Coming Soon...</h1></center>
<br><br>
