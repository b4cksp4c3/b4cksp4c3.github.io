<center><h1>General</h1></center>
<br>
<center><h2>2Warm [50]</h2></center>
<br>
Q: Can you convert the number 42 (base 10) to binary (base 2)?

A: This can be done any number of ways, I will use the ```bc``` command line tool
```
# echo 'obase=2; ibase=10; 42' | bc
101010
```
Flag:```picoCTF{101010}```
<br>
<center><h2>Lets Warm Up [50]</h2></center>
<br>
Q: If I told you a word started with 0x70 in hexadecimal, what would it start with in ASCII?

A: This can be easily done using python
```
>>> chr(0x70)
'p'
```
Flag:```picoCTF{p}```
<br>
<center><h2>Warmed Up [50]</h2></center>
<br>
Q: What is 0x3D (base 16) in decimal (base 10).

A: We can use ```bc``` for this again
```
# echo 'obase=10; ibase=16; 3D' | bc
61
```
Flag:```picoCTF{61}```
<br>
<center><h2>Bases [100]</h2></center>
<br>
Q: What does this bDNhcm5fdGgzX3IwcDM1 mean? I think it has something to do with bases.

A: This is a simple base64 string that we need to decode
```
# echo "bDNhcm5fdGgzX3IwcDM1" | base64 -d
l3arn_th2_r0p35
```
Flag:```picoCTF{l3arn_th2_r0p35}```
<br>
<center><h2>First Grep [100]</h2></center>
<br>
Q: Can you find the flag in file? This would be really tedious to look through manually, something tells me there is a better way. You can also find the file in /problems/first-grep_5_452e1c1630eb14b6753e9a155c3ae588 on the shell server.

A: Using a simple grep command we can find the flag
```
# grep pico file
picoCTF{grep_is_good_to_find_things_887251c6}
```
Flag:```picoCTF{grep_is_good_to_find_things_887251c6}```
<br>
<center><h2>Resources [100]</h2></center>
<br>
Q: We put together a bunch of resources to help you out on our website! If you go over there, you might even find a flag! https://picoctf.com/resources *[(link)](https://picoctf.com/resources)*

A: Navigating to the link towards the bottom of the page we should see the flag.

<center><img src="/picoctf2019/images/resources.png"></center>
<br>
Flag:```picoCTF{r3source_pag3_f1ag}```
<br>
<center><h2>strings it [100]</h2></center>
<br>
Q: Can you find the flag in *[file](/picoctf2019/files/strings_it)* without running it? You can also find the file in /problems/strings-it_2_865eec66d190ef75386fb14e15972126 on the shell server.

A: Using the <b>strings</b> command combined with <b>grep</b> gives us the flag
```
# strings strings | grep pico
picoCTF{5tRIng5_1T_d5b86184}
```
Flag:```picoCTF{5tRIng5_1T_d5b86184}```
<br>
<center><h2>what's a net cat? [100]</h2></center>
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
<center><h2>Based [200]</h2></center>
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
<center><h2>First Grep: Part II [200]</h2></center>
<br>
Q: Can you find the flag in /problems/first-grep--part-ii_0_b68f6a4e9cb3a7aad4090dea9dd80ce1/files on the shell server? Remember to use grep.

A: Simply read all of the files and ```grep``` for the flag
```
$ cat files*/* | grep pico
picoCTF{grep_r_to_find_this_e4fa3ba7}
```
Flag:```picoCTF{grep_r_to_find_this_e4fa3ba7}```
<br>
<center><h2>plumbing [200]</h2></center>
<br>
Q: Sometimes you need to handle process data outside of a file. Can you find a way to keep the output from this program and search for the flag? Connect to ```2019shell1.picoctf.com 18944```.

A: Again we can just use ```grep``` to grab the flag.
```
# nc 2019shell1.picoctf.com 18944 | grep pico
picoCTF{digital_plumb3r_1d5b7de7}
```
Flag:```picoCTF{digital_plumb3r_1d5b7de7}```
<br>
<center><h2>whats-the-difference [200]</h2></center>
<br>
Q: Can you spot the difference? *[kitters](/picoctf2019/files/kitters.jpg)* *[cattos](/picoctf2019/files/cattos.jpg)*. They are also available at /problems/whats-the-difference_0_00862749a2aeb45993f36cc9cf98a47a on the shell server

A: There are a few steps to this problem. First convert both of the photos to hex using ```xxd```
```
# xxd cattos.jpg > cattoshex
# xxd kitters.jpg > kittershex
```
Second, use the ```diff``` command to compare the files line by line and find the differences and output those to another file.
```
# diff cattoshex kittershex > diff.txt
```
Lastly, open the file and compare the lines two at a time and find the differences between them. Eventually you should be able to construct a flag.
Flag:```picoCTF{th2yr3_a5_d1ff3r3nt_4s_bu773r_4nd_j311y_aslkjfdsalkfslkflkjdsfdszmz10548}```
<br>
<center><h2>where-is-the-file [200]</h2></center>
<br>
Q: I've used a super secret mind trick to hide this file. Maybe something lies in /problems/where-is-the-file_6_8eae99761e71a8a21d3b82ac6cf2a7d0.

A: This question is very simple and must be done from the shell, the name of the question gives it away that the file is hidden. Run ```ls -la``` and you will see the file. then you can just ```cat``` it.
```
$ ls -la
total 80
drwxr-xr-x   2 root       root        4096 Sep 28 22:05 .
drwxr-x--x 684 root       root       69632 Oct  1 23:20 ..
-rw-rw-r--   1 hacksports hacksports    39 Sep 28 22:05 .cant_see_me

$ cat .cant_see_me
picoCTF{w3ll_that_d1dnt_w0RK_a88d16e4}
```
Flag:```picoCTF{w3ll_that_d1dnt_w0RK_a88d16e4}```
<br>
<center><h2>flag_shop [300]</h2></center>
<br>
Q: There's a flag shop selling stuff, can you buy a flag? *[Source](/picoctf2019/files/store.c)*. Connect with ```nc 2019shell1.picoctf.com 3967```.

Hint: Two's compliment can do some weird things when numbers get really big!

A: Reading the hint pretty much gives the answer. It tells us that there is an integer overflow in the second option of the program. Taking this information we can easily get the flag by entering ten 1's into the second option causing an integer overflow giving us the amount of money we need to buy the flag.
```
# nc 2019shell1.picoctf.com 3967
Welcome to the flag exchange
We sell flags

1. Check Account Balance

2. Buy Flags

3. Exit

 Enter a menu selection
2
Currently for sale
1. Defintely not the flag Flag
2. 1337 Flag
1
These knockoff Flags cost 900 each, enter desired quantity
1111111111

The final cost is: -727380068

Your current balance after transaction: 727381168

Welcome to the flag exchange
We sell flags

1. Check Account Balance

2. Buy Flags

3. Exit

 Enter a menu selection
2
Currently for sale
1. Defintely not the flag Flag
2. 1337 Flag
2
1337 flags cost 100000 dollars, and we only have 1 in stock
Enter 1 to buy one1
YOUR FLAG IS: picoCTF{m0n3y_bag5_cd0ead78}
```
Flag:```picoCTF{m0n3y_bag5_cd0ead78}```
<br>
<center><h2>mus1c</h2></center>
<br>
Q: I wrote you a *[song](/picoctf2019/files/lyrics.txt)*. Put it in the picoCTF{} flag format

A: Coming Soon...
