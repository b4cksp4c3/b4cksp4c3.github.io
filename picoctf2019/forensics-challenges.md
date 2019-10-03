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
