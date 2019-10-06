<center><h1>Reverse Engineering</h1></center>
<br>
<center><h1>vault-door-training [50]</h1></center>
<br>
<h2>Question</h2> Your mission is to enter Dr. Evil's laboratory and retrieve the blueprints for his Doomsday Project. The laboratory is protected by a series of locked vault doors. Each door is controlled by a computer and requires a password to open. Unfortunately, our undercover agents have not been able to obtain the secret passwords for the vault doors, but one of our junior agents obtained the source code for each vault's computer! You will need to read the source code for each level to figure out what the password is for that vault door. As a warmup, we have created a replica vault in our training facility. The source code for the training vault is here: *[VaultDoorTraining.java](/picoctf2019/files/VaultDoorTraining.java)*

<h2>Answer</h2> The password is located at the bottom of the source code
```
import java.util.*;

class VaultDoorTraining {
    public static void main(String args[]) {
        VaultDoorTraining vaultDoor = new VaultDoorTraining();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
        String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
        if (vaultDoor.checkPassword(input)) {
            System.out.println("Access granted.");
        } else {
            System.out.println("Access denied!");
        }
   }

    // The password is below. Is it safe to put the password in the source code?
    // What if somebody stole our source code? Then they would know what our
    // password is. Hmm... I will think of some ways to improve the security
    // on the other doors.
    //
    // -Minion #9567
    public boolean checkPassword(String password) {
        return password.equals("w4rm1ng_Up_w1tH_jAv4_87f51143e4b");
    }
}
```
Flag:```picoCTF{w4rm1ng_Up_w1tH_jAv4_87f51143e4b}```
<br>
<center><h1>vault-door-1 [100]</h1></center>
<br>
<h2>Question</h2> This vault uses some complicated arrays! I hope you can make sense of it, special agent. The source code for this vault is here: *[VaultDoor1.java](/picoctf2019/files/VaultDoor1.java)*

<h2>Answer</h2> Taking a look at the source code we can again see the password at the bottom but it is all scrambled and out of order.
```
import java.util.*;

class VaultDoor1 {
    public static void main(String args[]) {
        VaultDoor1 vaultDoor = new VaultDoor1();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
	String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
	}
    }

    // I came up with a more secure way to check the password without putting
    // the password itself in the source code. I think this is going to be
    // UNHACKABLE!! I hope Dr. Evil agrees...
    //
    // -Minion #8728
    public boolean checkPassword(String password) {
        return password.length() == 32 &&
               password.charAt(0)  == 'd' &&
               password.charAt(29) == '7' &&
               password.charAt(4)  == 'r' &&
               password.charAt(2)  == '5' &&
               password.charAt(23) == 'r' &&
               password.charAt(3)  == 'c' &&
               password.charAt(17) == '4' &&
               password.charAt(1)  == '3' &&
               password.charAt(7)  == 'b' &&
               password.charAt(10) == '_' &&
               password.charAt(5)  == '4' &&
               password.charAt(9)  == '3' &&
               password.charAt(11) == 't' &&
               password.charAt(15) == 'c' &&
               password.charAt(8)  == 'l' &&
               password.charAt(12) == 'H' &&
               password.charAt(20) == 'c' &&
               password.charAt(14) == '_' &&
               password.charAt(6)  == 'm' &&
               password.charAt(24) == '5' &&
               password.charAt(18) == 'r' &&
               password.charAt(13) == '3' &&
               password.charAt(19) == '4' &&
               password.charAt(21) == 'T' &&
               password.charAt(16) == 'H' &&
               password.charAt(27) == '3' &&
               password.charAt(30) == 'a' &&
               password.charAt(25) == '_' &&
               password.charAt(22) == '3' &&
               password.charAt(28) == 'b' &&
               password.charAt(26) == '0' &&
               password.charAt(31) == '0';
    }
}
```
This can be done by hand but since it is nicely numbered for us, we are able to do this from the terminal.
```
# grep 'password.charAt' VaultDoor1.java | tr -d " \t\r" | sed 's/^................//' | sort -n | sed 's/^.....//' | tr -d "'&;\n"
d35cr4mbl3_tH3_cH4r4cT3r5_03b7a0
```
Flag:```picoCTF{d35cr4mbl3_tH3_cH4r4cT3r5_03b7a0}```
<br>
<center><h1>asm1 [200]</h1></center>
<br>
<h1>Question</h1>What does asm1(0x76) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. *[Source](/picoctf2019/files/asm1.S)* located in the directory at /problems/asm1_0_b87970313ffbb5bcf4240e7c7b6c90cf.
<br>
<h1>Answer</h1>
Terms:
- cmp -> compare
- jg -> jump if greater than
- jne -> jump not equal to
- jmp -> jump
- add -> add

Now first we compare ```0x76``` to ```0x575```, if ```0x76``` is greater than ```0x575``` than we jump to ```0x50f <asm1+34>``` otherwise we continue. Next we compare ```0x76``` to ```0x76```, since they are equal, we will not follow the ```jne``` and continue down the stack. Now we will add ```0x11``` to our ```0x76``` giving us ```0x87```. Lastly, it tells us to jump to ```0x526 <asm1+57>``` which brings us to a final value of ```0x87```
```
asm1:
	<+0>:	push   ebp
	<+1>:	mov    ebp,esp
	<+3>:	cmp    DWORD PTR [ebp+0x8],0x575
	<+10>:	jg     0x50f <asm1+34>
	<+12>:	cmp    DWORD PTR [ebp+0x8],0x76
	<+16>:	jne    0x507 <asm1+26>
	<+18>:	mov    eax,DWORD PTR [ebp+0x8]
	<+21>:	add    eax,0x11
	<+24>:	jmp    0x526 <asm1+57>
	<+26>:	mov    eax,DWORD PTR [ebp+0x8]
	<+29>:	sub    eax,0x11
	<+32>:	jmp    0x526 <asm1+57>
	<+34>:	cmp    DWORD PTR [ebp+0x8],0x9d5
	<+41>:	jne    0x520 <asm1+51>
	<+43>:	mov    eax,DWORD PTR [ebp+0x8]
	<+46>:	sub    eax,0x11
	<+49>:	jmp    0x526 <asm1+57>
	<+51>:	mov    eax,DWORD PTR [ebp+0x8]
	<+54>:	add    eax,0x11
	<+57>:	pop    ebp
	<+58>:	ret    
```
Flag:```0x87```
<br>
<center><h1>vault-door-3 [200]</h1></center>
<br>
<h1>Question</h1>This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java
<br>
<h1>Answer</h1>
<center><h1>asm2 [250]</h1></center>
<br>
<h2>Question</h2>What does asm2(0x6,0x24) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. *[Source](/picoctf2019/files/asm1.S)* located in the directory at /problems/asm2_6_88bbaaae0b7723b33c39fce07d342e36.
<br>
<h2>Answers</h2>This is a little trickier since we have two arguments. Lets take a look at the assembly code.
```
asm2:
	<+0>:	push   ebp
	<+1>:	mov    ebp,esp
	<+3>:	sub    esp,0x10
	<+6>:	mov    eax,DWORD PTR [ebp+0xc]
	<+9>:	mov    DWORD PTR [ebp-0x4],eax
	<+12>:	mov    eax,DWORD PTR [ebp+0x8]
	<+15>:	mov    DWORD PTR [ebp-0x8],eax
	<+18>:	jmp    0x50c <asm2+31>
	<+20>:	add    DWORD PTR [ebp-0x4],0x1
	<+24>:	add    DWORD PTR [ebp-0x8],0xf9
	<+31>:	cmp    DWORD PTR [ebp-0x8],0x3c75
	<+38>:	jle    0x501 <asm2+20>
	<+40>:	mov    eax,DWORD PTR [ebp-0x4]
	<+43>:	leave  
	<+44>:	ret    
```
Lets brake this into pieces. Here we have our arguments,
```
<+6>:	mov    eax,DWORD PTR [ebp+0xc] <-- eax = 0x24
<+9>:	mov    DWORD PTR [ebp-0x4],eax <-- arg1 = 0x24
<+12>:	mov    eax,DWORD PTR [ebp+0x8] <-- eax = 0x6
<+15>:	mov    DWORD PTR [ebp-0x8],eax <-- = 0x6
```
Next we are going to jump to ```0x50c <asm2+31>``` which will compare ```[ebp-0x8]``` to ```0x3c75```. If ```[ebp-0x4]``` is less than ```0x501``` than we will jump to ```<asm2+20>```. As soon as arg2 is larger than ```0x3c75``` the program will terminate. Now lets take a look how this will happen after we jump to ```<asm2+20>```.
```
<+20>:	add    DWORD PTR [ebp-0x4],0x1 <--arg1 += 1 each time
<+24>:	add    DWORD PTR [ebp-0x8],0xf9 <-- arg2 += f9 each time
<+31>:	cmp    DWORD PTR [ebp-0x8],0x3c75 <-- arg2 compared to each time
```
This can be calculated by hand but it is much easier to do with python.
```
#!/usr/bin/env python

def asm2(arg1, arg2):
	while arg1 <= 0x3c75:
		arg2 += 1
		arg1 += 0xf9

	print hex(arg2)

asm2(0x6, 0x24)
```
Flag:```0x63```
<br>
<center><h1>vault-door-4 [250]</h1></center>
<br>
<h1>Question</h1>This vault uses ASCII encoding for the password. The source code for this vault is here: *[VaultDoor4.java](/picoctf2019/files/VaultDoor4.java)*
<br>
<h1>Answer</h1>In the source code there are comments stating that the password was converted into a bunch of different bases so that tells us what we have to do so lets take a look at that.
```
import java.util.*;

class VaultDoor4 {
    public static void main(String args[]) {
        VaultDoor4 vaultDoor = new VaultDoor4();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // I made myself dizzy converting all of these numbers into different bases,
    // so I just *know* that this vault will be impenetrable. This will make Dr.
    // Evil like me better than all of the other minions--especially Minion
    // #5620--I just know it!
    //
    //  .:::.   .:::.
    // :::::::.:::::::
    // :::::::::::::::
    // ':::::::::::::'
    //   ':::::::::'
    //     ':::::'
    //       ':'
    // -Minion #7781
    public boolean checkPassword(String password) {
        byte[] passBytes = password.getBytes();
        byte[] myBytes = {
            106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  ,
            0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,
            0142, 0131, 0164, 063 , 0163, 0137, 0142, 071 ,
            'e' , '9' , '2' , 'f' , '7' , '6' , 'a' , 'c' ,
        };
        for (int i=0; i<32; i++) {
            if (passBytes[i] != myBytes[i]) {
                return false;
            }
        }
        return true;
    }
}
```
Looking at the section of converted bytes we can label what kind of conversions we have.
```
yte[] myBytes = {
    106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  , <-- Decimal
    0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f, <-- Hex
    0142, 0131, 0164, 063 , 0163, 0137, 0142, 071 , <-- Octal(Remove the leading 0)
    'e' , '9' , '2' , 'f' , '7' , '6' , 'a' , 'c' , <-- Leave be
};
```
Now after converting everything we should get a value of ```jU5t_4_bUnCh_0f_bYt3s_b9e92f76ac```. We can test if this is the correct flag by compiling the code and using this flag as input.
```
# javac VaultDoor4.java
# java VaultDoor4
Enter vault password: picoCTF{jU5t_4_bUnCh_0f_bYt3s_b9e92f76ac}
Access granted.
```
Flag:```picoCTF{jU5t_4_bUnCh_0f_bYt3s_b9e92f76ac}```
<br>
<center><h1>asm3 [300]</h1></center>
<br>
<h1>Question</h1>What does asm3(0xdff83990,0xeeff29ae,0xfa706498) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. *[Source](/picoctf2019/files/asm3.S)* located in the directory at /problems/asm3_3_8aa3e17880273360f781adadc67a15f0.
<br>
<h1>Answer</h1> Assembly is by far not my strong area so the only way I could think of doing thing was to compile and run the code.
```
asm3:
	<+0>:	push   ebp
	<+1>:	mov    ebp,esp
	<+3>:	xor    eax,eax
	<+5>:	mov    ah,BYTE PTR [ebp+0xb]
	<+8>:	shl    ax,0x10
	<+12>:	sub    al,BYTE PTR [ebp+0xd]
	<+15>:	add    ah,BYTE PTR [ebp+0xe]
	<+18>:	xor    ax,WORD PTR [ebp+0x12]
	<+22>:	nop
	<+23>:	pop    ebp
	<+24>:	ret    
```
After doing some research I was able to find what I was looking for. Using *[this website](https://defuse.ca/online-x86-assembler.htm#disassembly)* I was able to get the bytecode.

Now, I am going to modify some C code I found online. I added my bytecode into the buffer and then I passed my parameters ```0xdff83990,0xeeff29ae,0xfa706498```. The final code should look something like this.
```
#include <stdio.h>

char shellcode[] = "\x55\x89\xE5\x31\xC0\x8A\x65\x0B\x66\xC1\xE0\x10\x2A\x45\x0D\x02\x65\x0E\x66\x33\x45\x12\x90\x5D\xC3";

int main(int argc, char **argv){
	int (*fp)(int, int, int);
	fp = (void *)shellcode;
	int ret = fp(0xdff83990,0xeeff29ae,0xfa706498);
	printf("ret = 0x%x\n", ret);
}
```
Now to compile this type
```
gcc asm3.c -o asm3 -fno-stack-protector -z execstack -no-pie -m32
```
If you get an error while compiling you try installing the gcc standard library
```
sudo apt-get install gcc-multilib
```
Now if we run the binary we should get the flag.
```
# ./asm3
ret = 0x5a7
```
Flag:```0x5a7```
<br>
<br><br><br>
