<center><h1>Web</h1></center>
<br>
<center><h2>Insp3ct0r</h2></center>
<br>
Q: Kishor Balan tipped us off that the following code may need inspection: https://2019shell1.picoctf.com/problem/11196/ (*[link](https://2019shell1.picoctf.com/problem/11196/)*) or http://2019shell1.picoctf.com:11196

A: You can find the flag in 3 parts by looking at the source code.  
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
<center><h2>dont-use-client-side</h2></center>
<br>
Q: Can you break into this super secure portal? https://2019shell1.picoctf.com/problem/32259/ (*[link](https://2019shell1.picoctf.com/problem/32259/)*) or http://2019shell1.picoctf.com:32259

A: Once again inspect the web page source code.
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
