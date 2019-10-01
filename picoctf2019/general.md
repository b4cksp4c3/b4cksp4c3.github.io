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
