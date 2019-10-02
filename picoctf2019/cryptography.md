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
<center><h2>rsa-pop-quiz</h2></center>
<br>
Q: Class, take your seats! It's PRIME-time for a quiz... ```nc 2019shell1.picoctf.com 48028```

A: Using the following python script we can get the flag.
```
#!/usr/bin/env python

from pwn import *

sh = remote('2019shell1.picoctf.com', 48028)

# https://stackoverflow.com/questions/4798654/modular-multiplicative-inverse-function-in-python
def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m

# question 1
q1 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')
q = int(q1[-5].split(' : ')[1])
p = int(q1[-4].split(' : ')[1])

sh.sendline('y')
sh.sendlineafter('n: ', str(p*q))

print 'question 1 done'

# question 2
q2 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')
p = int(q2[-5].split(' : ')[1])
n = int(q2[-4].split(' : ')[1])

sh.sendline('y')
sh.sendlineafter('q: ', str(n/p))

print 'question 2 done'

# question 3
q3 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')

sh.sendline('n')

print 'question 3 done'

# question 4
q4 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')

q = int(q4[-5].split(' : ')[1])
p = int(q4[-4].split(' : ')[1])

sh.sendline('y')
sh.sendlineafter('totient(n): ', str((p-1)*(q-1)))

print 'question 4 done'

# question 5
q5 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')

plaintext = int(q5[-6].split(' : ')[1])
e = int(q5[-5].split(' : ')[1])
n = int(q5[-4].split(' : ')[1])

sh.sendline('y')
sh.sendlineafter('ciphertext: ', str(pow(plaintext, e, n)))

print 'question 5 done'

# question 6
q6 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):')

sh.sendline('n')

print 'question 6 done'

# question 7
q7 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')

q = int(q7[-6].split(' : ')[1])
p = int(q7[-5].split(' : ')[1])
e = int(q7[-4].split(' : ')[1])

sh.sendline('y')
sh.sendlineafter('d: ', str(modinv(e, (p-1)*(q-1))))

print 'question 7 done'

# question 8
q8 = sh.recvuntil('IS THIS POSSIBLE and FEASIBLE? (Y/N):').split('\n')

p = int(q8[-7].split(' : ')[1])
ciphertext = int(q8[-6].split(' : ')[1])
e = int(q8[-5].split(' : ')[1])
n = int(q8[-4].split(' : ')[1])

q = n/p
d = modinv(e, (p-1)*(q-1))
m = pow(ciphertext, d, n)

sh.sendline('y')
sh.sendlineafter('plaintext: ', str(m))

print 'question 8 done'

flag = unhex(hex(m)[2:])

print 'flag: {}'.format(flag)
```
Running the program gives us the flag
```
# python rsa-pop-quiz.py
[+] Opening connection to 2019shell1.picoctf.com on port 48028: Done
question 1 done
question 2 done
question 3 done
question 4 done
question 5 done
question 6 done
question 7 done
question 8 done
flag: picoCTF{wA8_th4t$_ill3aGal..o4d21b3ca}
[*] Closed connection to 2019shell1.picoctf.com port 48028
```
Flag:```picoCTF{wA8_th4t$_ill3aGal..o4d21b3ca}```
<br>
<center><h2>miniRSA</h2></center>
<br>
Q: Lets decrypt this: *[ciphertext?](/picoctf2019/files/ciphertext)* Something seems a bit small

A. Using the following python script we can get the flag.
```
#!/usr/bin/python

from gmpy2 import *

get_context().precision=500

c = mpq(2205316413931134031074603746928247799030155221252519872649594750678791181631768977116979076832403970846785672184300449694813635798586699205901153799059293422365185314044451205091048294412538673475392478762390753946407342073522966852394341, 1)

print str(hex(int(cbrt(c))))[2:-1].decode('hex')
```
Now run it.
```
# python newdecrypt.py
picoCTF{n33d_a_lArg3r_e_0a41ef50}
```
Flag:```picoCTF{n33d_a_lArg3r_e_0a41ef50}```
<br>
<center><h2>waves over lambda</h2></center>
<br>
Q: We made alot of substitutions to encrypt this. Can you decrypt it? Connect with ```nc 2019shell1.picoctf.com 21903```.

A: When we connect we are given some ciphertext. Using *[quip quip](https://www.quipqiup.com/)* we can decrypt the cipher and get the flag.

Flag:```frequency_is_c_over_lambda_vlnhnasstm```
<br>
<center><h2>b00tl3gRSA2</h2></center>
<br>
Q: n RSA d is alot bigger than e, why dont we use d to encrypt instead of e? Connect with ```nc 2019shell1.picoctf.com 40480```.

A: Using the following python scripts below we can get the flag, just make sure to replace my n,e,c values with yours

<strong>rsa_2.py</strong>
```
#!/usr/bin/env python

from continued_fractions import *

n = 11374883649693698567484602251593739112580423066336624250521250743022361240344248522485536$922459153863654005572016339932595567472169098171553097104272706223624161016193120747434323351$874181289492759790152547943548539479777131389298719254598769956233468003596825702663960245959$2406969448795703778177897020689
e = 95840471833076939926748592662275459672792221386398836457197147379183021091989114418287203$251227858644294144327810286515707865823230454847641861696573624636263880611702839039660500484$389795714952404946969818017580479150313628328074244657376268810392298258992537654167191080221$024445575790426152412198300461
c = 97715717797644642262918833584370108356783860120582527427423086110559659601554951719025087$158031741632815442703965723147564587600460451302723954228278435659098765253244573147901581553$125120257028362883587968968901440324135776310794120227287607240296046014050177314249944433732$716302384712408300193549360953
def egcd(a, b):
    if a == 0: return (b, 0, 1)
    g, x, y = egcd(b % a, a)
    return (g, y - (b // a) * x, x)

def mod_inv(a, m):
    g, x, _ = egcd(a, m)
    return (x + m) % m

def isqrt(n):
    x = n
    y = (x + 1) // 2
    while y < x:
        x = y
        y = (x + n // x) // 2
    return x

def crack_rsa(e, n):
    frac = rational_to_contfrac(e, n)
    convergents = convergents_from_contfrac(frac)

    for (k, d) in convergents:
        if k != 0 and (e * d - 1) % k == 0:
            phi = (e * d - 1) // k
            s = n - phi + 1
            # check if x*x - s*x + n = 0 has integer roots
            D = s * s - 4 * n
            if D >= 0:
                sq = isqrt(D)
                if sq * sq == D and (s + sq) % 2 == 0: return d

d = crack_rsa(e, n)
m = hex(pow(c, d, n)).rstrip("L")[2:]
print(m.decode("hex"))
```
<strong>continued_fractions.py</strong>
```
#!/usr/bin/env python

def rational_to_contfrac(x,y):
    # Converts a rational x/y fraction into a list of partial quotients [a0, ..., an]
    a = x // y
    pquotients = [a]
    while a * y != x:
        x, y = y, x - a * y
        a = x // y
        pquotients.append(a)
    return pquotients

def convergents_from_contfrac(frac):
    # computes the list of convergents using the list of partial quotients
    convs = [];
    for i in range(len(frac)): convs.append(contfrac_to_rational(frac[0 : i]))
    return convs

def contfrac_to_rational (frac):
    # Converts a finite continued fraction [a0, ..., an] to an x/y rational.
    if len(frac) == 0: return (0,1)
    num = frac[-1]
    denom = 1
    for _ in range(-2, -len(frac) - 1, -1): num, denom = frac[_] * num + denom, num
    return (num, denom)
```
Running the main python function will output the flag
```
# python rsa_2.py
picoCTF{bad_1d3a5_9093280}
```
Flag:```picoCTF{bad_1d3a5_9093280}```
<br>
