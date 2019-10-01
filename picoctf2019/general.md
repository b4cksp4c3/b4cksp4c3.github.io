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

<center><img src="/pico2019pics/resources.png"></center>
<br>
Flag:```picoCTF{r3source_pag3_f1ag}```
