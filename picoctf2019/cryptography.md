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
<center><h2>caesar</h2></center>
<br>
Q: Decrypt this message. You can find the ciphertext in /problems/caesar_3_33108a6b0f87eb4b3606437d06290815 on the shell server.

Message: ```picoCTF{ynkooejcpdanqxeykjrubvtagp}```

A: You can use *[this](https://cryptii.com/pipes/caesar-cipher)* website to decrypt the flag.

Flag:```picoCTF{crossingtherubiconykwljqec}```
<br>
<center><h2>Flags</h2></center>
<br>
Q: What do the *[flags](/picoctf2019/files/flags.png)* mean?

A: The flags are from the International Code of Signals. Match each to flag and banner to each other and get the flag.

Flag:```PICOCTF{F1AG5AND5TUFF}```
<br>
<center><h2>Mr-Worldwide</h2></center>
<br>
Q: A musician left us a message. What's it mean?

Message: ```picoCTF{(35.028309, 135.753082)(46.469391, 30.740883)(39.758949, -84.191605)(41.015137, 28.979530)(24.466667, 54.366669)(3.140853, 101.693207)_(9.005401, 38.763611)(-3.989038, -79.203560)(52.377956, 4.897070)(41.085651, -73.858467)(57.790001, -152.407227)(31.205753, 29.924526)}```

A: Each combination of numbers correspond to coordinates around the world. You have to take the first letter of each city to creat the flag.
```
(35.028309, 135.753082)-->Kamigyo Ward, Kyoto
(46.469391, 30.740883)-->Odesa, Odessa Province, Ukraine
(39.758949, -84.191605)-->Dayton, Ohio
(41.015137, 28.979530)-->Istanbul, Turkey
(24.466667, 54.366669)-->Abu Dhabi, United Arab Emirates
(3.140853, 101.693207)-->Kuala Lumpur, Malaysia
_
(9.005401, 38.763611)-->Addis Ababa, Ethiopia
(-3.989038, -79.203560)-->Loja, Ecuador
(52.377956, 4.897070)-->Amsterdam, Netherlands
(41.085651, -73.858467)-->Stamford, CT
(57.790001, -152.407227)-->Kodiak, Alaska
(31.205753, 29.924526)-->Alexandria, Egypt
```
Flag:```picoCTF{KODIAK_ALASKA}```
