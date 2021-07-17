---
published: true
---
<img src="https://i.ibb.co/nCsMGX3/1.png" alt="1" border="0"> 

<P>Methodology</P>

<ul>
  <li>Enumeration by LFI</li>
  <li>Phpsessid and Jwt token forge</li>
  <li>unrestricted upload</li>
  <li>Database leak</li>
  <li>Binary file analysis</li>
  <li>Port forwarding</li>
  <li>Database dump with SQLMAP</li>
</ul>


<pre><span class="line">┌──(root💀m19o)-[~/HTB/Breadcrumbs]</span><br><span class="line">└─<span class="comment"># nmap -sV -v -p- --min-rate=10000 10.10.10.228</span></span><br><span class="line">PORT      STATE SERVICE       VERSION</span><br><span class="line">22/tcp    open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)</span><br><span class="line">80/tcp    open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1h PHP/8.0.1)</span><br><span class="line">135/tcp   open  msrpc         Microsoft Windows RPC</span><br><span class="line">139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn</span><br><span class="line">443/tcp   open  ssl/http      Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1h PHP/8.0.1)</span><br><span class="line">445/tcp   open  microsoft-ds?</span><br><span class="line">3306/tcp  open  mysql?</span><br><span class="line">5040/tcp  open  unknown</span><br><span class="line">7680/tcp  open  pando-pub?</span><br><span class="line">49664/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">49665/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">49666/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">49667/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">49668/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">49669/tcp open  msrpc         Microsoft Windows RPC</span><br><span class="line">1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :</span><br><span class="line">SF-Port3306-TCP:V=7.91%I=7%D=2/23%Time=6034FA27%P=x86_64-pc-linux-gnu%r(RP</span><br><span class="line">SF:CCheck,4A,<span class="string">"F\0\0\x01\xffj\x04Host\x20'10\.10\.14\.12'\x20is\x20not\x20a</span></span><br><span class="line"><span class="string">SF:llowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server"</span>)%r(X11Probe</span><br><span class="line">SF:,4A,<span class="string">"F\0\0\x01\xffj\x04Host\x20'10\.10\.14\.12'\x20is\x20not\x20allowed</span></span><br><span class="line"><span class="string">SF:\x20to\x20connect\x20to\x20this\x20MariaDB\x20server"</span>);</span><br><span class="line">Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows</span><br></pre>


<h1>Enumeration</h1>
<br>
<img src="https://i.ibb.co/pPJx49Z/2.png" alt="2" border="0">

<P>As a penetartion tester you should analyze the application to understand the functionality of it</P>
<p>So now let`s check what that button does</p>

<img src="https://i.ibb.co/hgg9FsV/3.png" alt="3" border="0">

<p>From that page we can understand that the application is for reading books and that button checks for books with title and author name</p>
<p>Let`s see how does the application search for books</p>

<img src="https://i.ibb.co/WBJfXR3/4.png" alt="4" border="0">

<p>So i started to test the searching with random word, in my case i used test to understand the behavior</p>
<p>I got nothing from that, so i will start my proxy "Burpsuite" to intercept that request</p>





