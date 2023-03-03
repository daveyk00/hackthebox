Connect the VPN, spawn the VM.<br>
10.129.231.175



### TASK 1

Which service version is found to be running on port 80?

```
└─# nmap -sS -n -Pn --top-ports 100 -sV --osscan-guess 10.129.231.175
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 01:51 EST
Stats: 0:00:10 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 50.00% done; ETC: 01:51 (0:00:01 remaining)
Nmap scan report for 10.129.231.175
Host is up (0.23s latency).
Not shown: 99 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.46 seconds
```

Answer: Nginx 1.14.2

### TASK 2

What is the 3-digit HTTP status code returned when you visit http://{machine IP}/?

```
└─# curl -v http://10.129.231.175/
*   Trying 10.129.231.175:80...
* Connected to 10.129.231.175 (10.129.231.175) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.129.231.175
> User-Agent: curl/7.85.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 302 Found
< Server: nginx/1.14.2
< Date: Fri, 03 Mar 2023 06:58:14 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Set-Cookie: PHPSESSID=s5hfbvblm5dtlsfkmc0b530hjq; expires=Fri, 03-Mar-2023 07:58:14 GMT; Max-Age=3600; path=/; domain=10.129.231.175; HttpOnly; SameSite=Lax
< Location: http://ignition.htb/
< Pragma: no-cache
< Cache-Control: max-age=0, must-revalidate, no-cache, no-store
< Expires: Thu, 03 Mar 2022 06:58:14 GMT
< Content-Security-Policy-Report-Only: font-src data: 'self' 'unsafe-inline'; form-action secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; frame-ancestors 'self' 'unsafe-inline'; frame-src fast.amc.demdex.net secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com www.paypal.com www.sandbox.paypal.com player.vimeo.com *.youtube.com 'self' 'unsafe-inline'; img-src assets.adobedtm.com amcglobal.sc.omtrdc.net dpm.demdex.net cm.everesttech.net widgets.magentocommerce.com data: www.googleadservices.com www.google-analytics.com www.paypalobjects.com t.paypal.com www.paypal.com fpdbs.paypal.com fpdbs.sandbox.paypal.com *.vimeocdn.com i.ytimg.com s.ytimg.com data: 'self' 'unsafe-inline'; script-src assets.adobedtm.com secure.authorize.net test.authorize.net www.googleadservices.com www.google-analytics.com www.paypalobjects.com js.braintreegateway.com www.paypal.com geostag.cardinalcommerce.com 1eafstag.cardinalcommerce.com geoapi.cardinalcommerce.com 1eafapi.cardinalcommerce.com songbird.cardinalcommerce.com includestest.ccdc02.com www.sandbox.paypal.com t.paypal.com s.ytimg.com www.googleapis.com vimeo.com www.vimeo.com *.vimeocdn.com www.youtube.com video.google.com 'self' 'unsafe-inline' 'unsafe-eval'; style-src getfirebug.com 'self' 'unsafe-inline'; object-src 'self' 'unsafe-inline'; media-src 'self' 'unsafe-inline'; manifest-src 'self' 'unsafe-inline'; connect-src dpm.demdex.net amcglobal.sc.omtrdc.net www.google-analytics.com geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; child-src http: https: blob: 'self' 'unsafe-inline'; default-src 'self' 'unsafe-inline' 'unsafe-eval'; base-uri 'self' 'unsafe-inline';
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< X-Frame-Options: SAMEORIGIN
< 
* Connection #0 to host 10.129.231.175 left intact
```

Answer: 302

### TASK 3

What is the virtual host name the webpage expects to be accessed by?

Answer: ignition.htb

### TASK 4

What is the full path to the file on a Linux computer that holds a local list of domain name to IP address pairs?

https://tldp.org/LDP/solrhe/Securing-Optimizing-Linux-RH-Edition-v1.3/chap9sec95.html
https://en.wikipedia.org/wiki/Hosts_(file)

Answer: /etc/hosts

### TASK 5

Use a tool to brute force directories on the webserver. What is the full URL to the Magento login page?

Edit the hosts file:

```
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.129.231.175  ignition.htb 
```

Run dirbuster

```
# dirb http://ignition.htb /usr/share/wordlists/dirb/common.txt

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Mar  3 02:08:33 2023
URL_BASE: http://ignition.htb/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://ignition.htb/ ----
+ http://ignition.htb/0 (CODE:200|SIZE:25803)                                                                                                                                                                                              
+ http://ignition.htb/admin (CODE:200|SIZE:7092)                                                                                                                                                                                           
+ http://ignition.htb/catalog (CODE:302|SIZE:0)                                                                                                                                                                                            
+ http://ignition.htb/checkout (CODE:302|SIZE:0)                                                                                                                                                                                           
+ http://ignition.htb/cms (CODE:200|SIZE:25817)                                                                                                                                                                                            
+ http://ignition.htb/contact (CODE:200|SIZE:28673)                                                                                                                                                                                        
+ http://ignition.htb/enable-cookies (CODE:200|SIZE:27176)  
```

Answer: http://ignition.htb/admin

### TASK 6

Look up the password requirements for Magento and also try searching for the most commong passwords of 2023. Which password provides access to the admin account?

Answer: qwerty123

### SUBMIT FLAG

Submit root flag
Answer: 797d6c988d9dc5865e010b9410f247e0