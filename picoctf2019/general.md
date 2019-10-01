<center>2Warm</center>
<br>
Q: Can you convert the number 42 (base 10) to binary (base 2)?

A: This can be done any number of ways, I will use the <b>bc</b> command line tool
```
# echo 'obase=2; ibase=10; 42' | bc
101010

picoCTF{101010}
```
