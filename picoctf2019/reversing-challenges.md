<center><h1>Reverse Engineering</h1></center>
<br>
<center>vault-door-training [50]<h1></h1></center>
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
cmp -> compare
jg -> jump if greater than
jne -> jump not equal to
jmp -> jump
add -> add

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
<br><br><br>
