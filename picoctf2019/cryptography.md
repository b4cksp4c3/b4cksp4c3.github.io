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

A: Each combination of numbers correspond to coordinates around the world. You have to take the first letter of each city to create the flag.
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
<br>
<center><h2>Tapping</h2></center>
<br>
Q: Theres tapping coming in from the wires. What's it saying ```nc 2019shell1.picoctf.com 37920```.

A: Connecting to the server with give us some morse code which we can decode using *[this](http://www.unit-conversion.info/texttools/morse-code/)*. According to the hint the flag is in all caps.

Flag:```PICOCTF{M0RS3C0D31SFUN583900981}```
<br>
<center><h2>la cifra de</h2></center>
<br>
Q: I found this cipher in an old book. Can you figure out what it says? Connect with ```nc 2019shell1.picoctf.com 1172```.

Connecting to the server we are given an encrypted message.
```
Ne iy nytkwpsznyg nth it mtsztcy vjzprj zfzjy rkhpibj nrkitt ltc tnnygy ysee itd tte cxjltk

Ifrosr tnj noawde uk siyyzre, yse Bnretèwp Cousex mls hjpn xjtnbjytki xatd eisjd

Iz bls lfwskqj azycihzeej yz Brftsk ip Volpnèxj ls oy hay tcimnyarqj dkxnrogpd os 1553 my Mnzvgs Mazytszf Merqlsu ny hox moup Wa inqrg ipl. Ynr. Gotgat Gltzndtg Gplrfdo

Ltc tnj tmvqpmkseaznzn uk ehox nivmpr g ylbrj ts ltcmki my yqtdosr tnj wocjc hgqq ol fy oxitngwj arusahje fuw ln guaaxjytrd catizm tzxbkw zf vqlckx hizm ceyupcz yz tnj fpvjc hgqqpohzCZK{m311a50_0x_a1rn3x3_h1ah3xflc148k7}

Yse lncsz bplr-izcarpnzjo dkxnroueius zf g uzlefwpnfmeznn cousex mzwkapr, cfd mgip axtfnj 1467 gj Lkty Bgyeiyyl Argprzn.

Ehk Atgksèce Inahkw ts zmprkkzrk xzmkytmkx narqpd zmp Argprzn Oiyh zr Gqmexyt Cousex.

Ny 1508, Jumlntjd Txnehkrtuy nyvkseej yse yt-narqpd zfmurf ceiyl (a sferoc zf ymtfzjo arusahjes) zmlt ctflj qltkw me g hciznnar hzmvtyety zf zmp Volpnèxj Nivmpr.

Hjwlgxz’s yjnoti moupwez fapkfcej ny 1555 ay f notytnafeius zf zmp fowdt. Zmp lubpr nfwvkx zf zmp arusahjes gwp nub dhokeej wpgaqlrrd, muz yse gqahggpty fyd zmp itipx rjetkwd axj xidjo be rpatx zf g ryestyii ppy vmcayj, hhohs cgs me jnqfkwpnz bttn jlcn hzrxjdpusoety.
```
 We can use *[this website](https://www.guballa.de/vigenere-solver)* to decrypt the message and get the flag.
 ```
 For the implementation of this cipher a table is formed by sliding the lower half of an ordinary alphabet for an apparently random
number of places with respect to the upper halfpicoCTF{b311a50_0r_v1gn3r3_c1ph3raac148e7}

The first well-documented description of a polyalphabetic cipher however, was made around 1467 by Leon Battista Alberti.

The Vigenère Cipher is therefore sometimes called the Alberti Disc or Alberti Cipher.

In 1508, Johannes Trithemius invented the so-called tabula recta (a matrix of shifted alphabets) that would later be a critical com
ponent of the Vigenère Cipher.

Bellaso’s second booklet appeared in 1555 as a continuation of the first. The lower halves of the alphabets are now shifted regular
ly, but the alphabets and the index letters are mixed by means of a mnemonic key phrase, which can be different with each correspon
dent.
```
Flag:```picoCTF{b311a50_0r_v1gn3r3_c1ph3raac148e7}```
<br>
