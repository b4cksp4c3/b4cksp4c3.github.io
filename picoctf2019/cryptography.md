<center><h1>Cryptography</h1></center>
<br><br>
<center><h2>The Numbers</h2></center>
<br>
Q: The *[numbers](/picoctf2019/files/the_numbers.png)*... what do they mean?

A: Pretty obvious to see that each number corresponds to the letter of the alphabet. Slowly but surely you should get a flag.

Flag:```PICOCTF{THENUMBERSMASON}```
<br>
<center><h2>13</h2></center>
<br>
Q: Cryptography can be easy, do you know what ROT13 is? ```cvpbPGS{abg_gbb_onq_bs_n_ceboyrz}```

A: So a ROT 13. This can be done by hand, a website, or from the terminal. I will use the terminal
```
# echo "cvpbPGS{abg_gbb_onq_bs_n_ceboyrz}" | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
picoCTF{not_too_bad_of_a_problem}
```
Flag:```picoCTF{not_too_bad_of_a_problem}```
<br>
<center><h2>Easy1</h2></center>
<br>
Q: The one time pad can be cryptographically secure, but not when you know the key. Can you solve this? We've given you the encrypted flag, key, and a table to help ```UFJKXQZQUNB``` with the key of ```SOLVECRYPTO```. Can you use this table to solve it?

A. Using the table below we can decode the message and get the flag
```
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
  +----------------------------------------------------
A | A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
B | B C D E F G H I J K L M N O P Q R S T U V W X Y Z A
C | C D E F G H I J K L M N O P Q R S T U V W X Y Z A B
D | D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
E | E F G H I J K L M N O P Q R S T U V W X Y Z A B C D
F | F G H I J K L M N O P Q R S T U V W X Y Z A B C D E
G | G H I J K L M N O P Q R S T U V W X Y Z A B C D E F
H | H I J K L M N O P Q R S T U V W X Y Z A B C D E F G
I | I J K L M N O P Q R S T U V W X Y Z A B C D E F G H
J | J K L M N O P Q R S T U V W X Y Z A B C D E F G H I
K | K L M N O P Q R S T U V W X Y Z A B C D E F G H I J
L | L M N O P Q R S T U V W X Y Z A B C D E F G H I J K
M | M N O P Q R S T U V W X Y Z A B C D E F G H I J K L
N | N O P Q R S T U V W X Y Z A B C D E F G H I J K L M
O | O P Q R S T U V W X Y Z A B C D E F G H I J K L M N
P | P Q R S T U V W X Y Z A B C D E F G H I J K L M N O
Q | Q R S T U V W X Y Z A B C D E F G H I J K L M N O P
R | R S T U V W X Y Z A B C D E F G H I J K L M N O P Q
S | S T U V W X Y Z A B C D E F G H I J K L M N O P Q R
T | T U V W X Y Z A B C D E F G H I J K L M N O P Q R S
U | U V W X Y Z A B C D E F G H I J K L M N O P Q R S T
V | V W X Y Z A B C D E F G H I J K L M N O P Q R S T U
W | W X Y Z A B C D E F G H I J K L M N O P Q R S T U V
X | X Y Z A B C D E F G H I J K L M N O P Q R S T U V W
Y | Y Z A B C D E F G H I J K L M N O P Q R S T U V W X
Z | Z A B C D E F G H I J K L M N O P Q R S T U V W X Y
```
Flag:```picoCTF{CRYPTOISFUN}```
<br>
