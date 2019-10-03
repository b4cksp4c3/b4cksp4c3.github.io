<center><h1>Forensics</h1></center>
<br>
<center><h2>Glory of the Garden [50]</h2></center>
<br>
Q: This *[garden](/picoctf2019/images/garden.jpg)* contains more than it seems. You can also find the file in /problems/glory-of-the-garden_4_cf9f4aaf458caf5268f8cf0a6465eb98 on the shell server.

A: Using ```strings``` and ```grep``` we can find the flag hidden in the image.
```
# strings garden.jpg | grep pico
Here is a flag "picoCTF{more_than_m33ts_the_3y36BCA684D}"
```
Flag:```picoCTF{more_than_m33ts_the_3y36BCA684D}```
<br>
<center><h2>unzip [50]</h2></center>
<br>
Q: Can you *[unzip](/picoctf2019/files/flag.zip)* this file and get the flag?

A: Unzipping the file will yield a photo with the flag printed on it
```
# unzip flag.zip
Archive:  flag.zip
  inflating: flag.png                
```
<center><img src="/picoctf2019/images/unzipping.png"></center>
<br>
Flag:```picoCTF{unz1pp1ng-1s-3a5y}```
<br>
<center><h2>So Meta [150]</h2></center>
<br>
Q: Find the flag in this *[picture](/picoctf2019/images/pico_img.png)*. You can also find the file in /problems/so-meta_2_da856426d694a4f0637bf1b169d8524e.

A: Using ```Exiftool``` we can get the flag easily.
```
# exiftool pico_img.png | grep picoCTF
Artist                          : picoCTF{s0_m3ta_3d6ced35}
```
Flag:```picoCTF{s0_m3ta_3d6ced35}```
<br>
<center><h2>What Lies Within [150]</h2></center>
<br>
Q: Theres something in the *[building](/picoctf2019/images/buildings.png)*. Can you retrieve the flag?

A: We can get the flag by decoding the images using *[this](https://stylesuxx.github.io/steganography/)* website.

<center><img src="/picoctf2019/images/whatlieswithinflag.png"></center>
<br>
Flag:```picoCTF{h1d1ng_1n_th3_b1t5}```
<br>
