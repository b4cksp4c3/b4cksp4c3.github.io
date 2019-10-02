<center><h1>General</h1></center>
<br>
<center><h3>2Warm</h3></center>
<br>
Q: Can you convert the number 42 (base 10) to binary (base 2)?

A: This can be done any number of ways, I will use the <b>bc</b> command line tool
```
# echo 'obase=2; ibase=10; 42' | bc
101010
```
Flag:```picoCTF{101010}```
<br>
<center><h3>Lets Warm Up</h3></center>
<br>
Q: If I told you a word started with 0x70 in hexadecimal, what would it start with in ASCII?

A: This can be easily done using python
```
>>> chr(0x70)
'p'
```
Flag:```picoCTF{p}```
<br>
<center><h3>Warmed Up</h3></center>
<br>
Q: What is 0x3D (base 16) in decimal (base 10).

A: We can use <b>bc</b> for this again
```
# echo 'obase=10; ibase=16; 3D' | bc
61
```
Flag:```picoCTF{61}```
<br>
<center><h3>Bases</h3></center>
<br>
Q: What does this bDNhcm5fdGgzX3IwcDM1 mean? I think it has something to do with bases.

A: This is a simple base64 string that we need to decode
```
# echo "bDNhcm5fdGgzX3IwcDM1" | base64 -d
l3arn_th3_r0p35
```
Flag:```picoCTF{l3arn_th3_r0p35}```
<br>
<center><h3>First Grep</h3></center>
<br>
Q: Can you find the flag in file? This would be really tedious to look through manually, something tells me there is a better way. You can also find the file in /problems/first-grep_5_452e1c1630eb14b6753e9a155c3ae588 on the shell server.

A: Using a simple grep command we can find the flag
```
# grep pico file
picoCTF{grep_is_good_to_find_things_887251c6}
```
Flag:```picoCTF{grep_is_good_to_find_things_887251c6}```
<br>
<center><h3>Resources</h3></center>
<br>
Q: We put together a bunch of resources to help you out on our website! If you go over there, you might even find a flag! https://picoctf.com/resources *[(link)](https://picoctf.com/resources)*

A: Navigating to the link towards the bottom of the page we should see the flag.

<center><img src="/picoctf2019/images/resources.png"></center>
<br>
Flag:```picoCTF{r3source_pag3_f1ag}```
<br>
<center><h3>strings it</h3></center>
<br>
Q: Can you find the flag in *[file](/picoctf2019/files/strings_it)* without running it? You can also find the file in /problems/strings-it_2_865eec66d190ef75386fb14e15972126 on the shell server.

A: Using the <b>strings</b> command combined with <b>grep</b> gives us the flag
```
# strings strings | grep pico
picoCTF{5tRIng5_1T_d5b86184}
```
Flag:```picoCTF{5tRIng5_1T_d5b86184}```
<br>
<center><h3>what's a net cat?</h3></center>
<br>
Q: Using netcat (nc) is going to be pretty important. Can you connect to ```2019shell1.picoctf.com``` at port ```4158``` to get the flag?

A: Just use netcat to connect and it will spit out the flag
```
# nc 2019shell1.picoctf.com 4158
You're on your way to becoming the net cat master
picoCTF{nEtCat_Mast3ry_700da9c7}
```
Flag:```picoCTF{nEtCat_Mast3ry_700da9c7}```
<br>
<center><h3>Based</h3></center>
<br>
Q: To get truly 1337, you must understand different data encodings, such as hexadecimal or binary. Can you get the flag from this program to prove you are on the way to becoming 1337? Connect with ```nc 2019shell1.picoctf.com 20836```

A: Answer can be found using a couple websites. Upon initial connection it gives us a string of bits and asks us what the word is. Luckily it gives us the answer for the first one, in my case its ```container```. Next it spits out some numbers in octal. Using *[this](http://www.unit-conversion.info/texttools/octal/)* webiste you can convert the octal to text. In my case it was ```table```. Lastly it spits out a string of hex. We can use *[this](http://www.unit-conversion.info/texttools/hexadecimal/)* website to decode the hex to a string, in my case it was ```container```.
```
# nc 2019shell1.picoctf.com 20836
Let us see how data is stored
container
Please give the 01100011 01101111 01101110 01110100 01100001 01101001 01101110 01100101 01110010 as a word.
...
you have 45 seconds.....

Input:
container
Please give me the  164 141 142 154 145 as a word.
Input:
table
Please give me the 636f6e7461696e6572 as a word.
Input:
container
You've beaten the challenge
Flag: picoCTF{learning_about_converting_values_6cdcad0d}
```
Flag: ```picoCTF{learning_about_converting_values_6cdcad0d}```
<br>
