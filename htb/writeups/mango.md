<center><h1>Mango</h1></center>
<br><br>
<center><<h2>Summary></h2></center>
- We see the ip does not match the name on the certificate.
- Adding ```staging-orders.mango.htb``` to our /etc/hosts file will reveal a login page
- With the boxes name being Mango, after trial and error I came to the conclusion that the box was running MongoDB and that I would need to exploit that
- Using an authentication bypass exploit we can then write a python script to get some credentials
- Using those credentials to SSH in, I run some enumeration scripts and immediately see some unusual SUID binaries which I use to can a root shell
<br><br>
