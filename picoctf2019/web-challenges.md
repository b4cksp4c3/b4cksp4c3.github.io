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
