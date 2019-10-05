<center><h1>Forensics</h1></center>
<br>
<center><h1>Glory of the Garden [50]</h1></center>
<br>
<h2>Question</h2>This *[garden](/picoctf2019/images/garden.jpg)* contains more than it seems. You can also find the file in /problems/glory-of-the-garden_4_cf9f4aaf458caf5268f8cf0a6465eb98 on the shell server.

<h2>Answer</h2>Using ```strings``` and ```grep``` we can find the flag hidden in the image.
```
# strings garden.jpg | grep pico
Here is a flag "picoCTF{more_than_m33ts_the_3y36BCA684D}"
```
Flag:```picoCTF{more_than_m33ts_the_3y36BCA684D}```
<br>
<center><h1>unzip [50]</h1></center>
<br>
<h2>Question</h2>Can you *[unzip](/picoctf2019/files/flag.zip)* this file and get the flag?

<h2>Answer</h2>Unzipping the file will yield a photo with the flag printed on it
```
# unzip flag.zip
Archive:  flag.zip
  inflating: flag.png                
```
<center><img src="/picoctf2019/images/unzipping.png"></center>
<br>
Flag:```picoCTF{unz1pp1ng-1s-3a5y}```
<br>
<center><h1>So Meta [150]</h1></center>
<br>
<h2>Question</h2>Find the flag in this *[picture](/picoctf2019/images/pico_img.png)*. You can also find the file in /problems/so-meta_2_da856426d694a4f0637bf1b169d8524e.

<h2>Answer</h2>Using ```Exiftool``` we can get the flag easily.
```
# exiftool pico_img.png | grep picoCTF
Artist                          : picoCTF{s0_m3ta_3d6ced35}
```
Flag:```picoCTF{s0_m3ta_3d6ced35}```
<br>
<center><h1>What Lies Within [150]</h1></center>
<br>
<h2>Question</h2>Theres something in the *[building](/picoctf2019/images/buildings.png)*. Can you retrieve the flag?

<h2>Answer</h2>We can get the flag by decoding the images using *[this](https://stylesuxx.github.io/steganography/)* website.

<center><img src="/picoctf2019/images/whatlieswithinflag.png"></center>
<br>
Flag:```picoCTF{h1d1ng_1n_th3_b1t5}```
<br>
<center><h1> extensions [150]</h1></center>
<br>
<h2>Question</h2>This is a really weird text file *[TXT](/picoctf2019/files/flag.txt)*? Can you find the flag?

<h2>Answer</h2>Using the ```file``` command we can see that the file is not a ```txt``` file but instead a ```png``` file. By changing the extension to ```.png``` we can look at the picture a read the flag.
```
# file flag.txt
flag.txt: PNG image data, 1697 x 608, 8-bit/color RGB, non-interlaced

# mv flag.txt flag.png
```

<center><img src="/picoctf2019/images/extensionsflag.png"></center>
<br>
Flag:```picoCTF{now_you_know_about_extensions}```
<br>
<center><h1>shark on wire 1 [150]</h1></center>
<br>
<h2>Question</h2>We found this *[packet capture](/picoctf2019/files/capture.pcap)*. Recover the flag. You can also find the file in /problems/shark-on-wire-1_0_13d709ec13952807e477ba1b5404e620.

<h2>Answer</h2>By setting an ip filter for ```10.0.0.12``` there will be 27 packets left, each containing 1 letter of the flag.

<center><img src="/picoctf2019/images/sharkonthewire1.png"></center>

Flag:```picoCTF{StaT31355_636f6e6e}```
<br>
