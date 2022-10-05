Connect the VPN, spawn the VM.<br>
10.129.153.39

### TASK 1
What nmap scanning switch employs the use of default scripts during a scan?

```
nmap --help
```

```
SCRIPT SCAN:
  -sC: equivalent to --script=default
  --script=<Lua scripts>: <Lua scripts> is a comma separated list of
           directories, script-files or script-categories
  --script-args=<n1=v1,[n2=v2,...]>: provide arguments to scripts
  --script-args-file=filename: provide NSE script args in a file
  --script-trace: Show all data sent and received
  --script-updatedb: Update the script database.
  --script-help=<Lua scripts>: Show help about scripts.
           <Lua scripts> is a comma-separated list of script-files or
           script-categories.
```

Answer: -sC

### TASK 2

What service version is found to be running on port 21?

```
# nmap -sC -sS -sV 10.129.153.39 -n -Pn -p 21
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 15:11 EDT
Nmap scan report for 10.129.153.39
Host is up (0.23s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.106
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.57 seconds
```

Answer: vsftpd 3.0.3

### TASK 3

What FTP code is returned to us for the "Anonymous FTP login allowed" message?

```
# ftp 10.129.153.39 
Connected to 10.129.153.39.
220 (vsFTPd 3.0.3)
Name (10.129.153.39:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

Answer: 230


### TASK 4

What command can we use to download the files we find on the FTP server?

```
ftp> ?
Commands may be abbreviated.  Commands are:

!               case            dir             fget            idle            mdelete         modtime         ntrans          progress        rcvbuf          rmdir           sndbuf          type
$               cd              disconnect      form            image           mdir            more            open            prompt          recv            rstatus         status          umask
account         cdup            edit            ftp             lcd             mget            mput            page            proxy           reget           runique         struct          unset
append          chmod           epsv            gate            less            mkdir           mreget          passive         put             remopts         send            sunique         usage
ascii           close           epsv4           get             lpage           mls             msend           pdir            pwd             rename          sendport        system          user
bell            cr              epsv6           glob            lpwd            mlsd            newer           pls             quit            reset           set             tenex           verbose
binary          debug           exit            hash            ls              mlst            nlist           pmlsd           quote           restart         site            throttle        xferbuf
bye             delete          features        help            macdef          mode            nmap            preserve        rate            rhelp           size            trace           ?
ftp> 
```

Answer: get

### TASK 5

What is one of the higher-privilege sounding usernames in the list we retrieved?

```
ftp> page allowed.userlist
```

```
aron
pwnmeow
egotisticalsw
admin
```

Answer: admin

### TASK 6

What version of Apache HTTP Server is running on the target host?

```
# nmap -sC -sS -sV 10.129.153.39 -n -Pn -p 80
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 15:18 EDT
Nmap scan report for 10.129.153.39
Host is up (0.23s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
|_http-server-header: Apache/2.4.41 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.39 seconds
```

Answer: 2.4.41

### TASK 7

What is the name of a handy web site analysis plug-in we can install in our browser?

Answer: wappalyzer

### TASK 8

What switch can we use with gobuster to specify we are looking for specific filetypes?

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
# gobuster dir --help
Uses directory/file enumeration mode

Usage:
  gobuster dir [flags]

Flags:
  -f, --add-slash                       Append / to each request
  -c, --cookies string                  Cookies to use for the requests
  -d, --discover-backup                 Upon finding a file search for backup files
      --exclude-length ints             exclude the following content length (completely ignores the status). Supply multiple times to exclude multiple sizes.
  -e, --expanded                        Expanded mode, print full URLs
  -x, --extensions string               File extension(s) to search for
  -r, --follow-redirect                 Follow redirects
  -H, --headers stringArray             Specify HTTP headers, -H 'Header1: val1' -H 'Header2: val2'
  -h, --help                            help for dir
      --hide-length                     Hide the length of the body in the output
  -m, --method string                   Use the following HTTP method (default "GET")
  -n, --no-status                       Don't print status codes
  -k, --no-tls-validation               Skip TLS certificate verification
  -P, --password string                 Password for Basic Auth
      --proxy string                    Proxy to use for requests [http(s)://host:port]
      --random-agent                    Use a random User-Agent string
  -s, --status-codes string             Positive status codes (will be overwritten with status-codes-blacklist if set)
  -b, --status-codes-blacklist string   Negative status codes (will override status-codes if set) (default "404")
      --timeout duration                HTTP Timeout (default 10s)
  -u, --url string                      The target URL
  -a, --useragent string                Set the User-Agent string (default "gobuster/3.1.0")
  -U, --username string                 Username for Basic Auth
      --wildcard                        Force continued operation when wildcard found

Global Flags:
      --delay duration    Time each thread waits between requests (e.g. 1500ms)
      --no-error          Don't display errors
  -z, --no-progress       Don't display progress
  -o, --output string     Output file to write results to (defaults to stdout)
  -p, --pattern string    File containing replacement patterns
  -q, --quiet             Don't print the banner and other noise
  -t, --threads int       Number of concurrent threads (default 10)
  -v, --verbose           Verbose output (errors)
  -w, --wordlist string   Path to the wordlist
```

Answer: -x


### TASK 9

What file have we found that can provide us a foothold on the target?

```
# gobuster dir -u 10.129.153.39 -w /usr/share/wordlists/wfuzz/general/admin-panels.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.153.39
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/admin-panels.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/10/05 15:32:15 Starting gobuster in directory enumeration mode
===============================================================
/login.php            (Status: 200) [Size: 1577]
===============================================================
2022/10/05 15:32:20 Finished
===============================================================
```

Answer: login.php


### SUBMIT FLAG

Submit root flag

```
# ftp 10.129.153.39 
Connected to 10.129.153.39.
220 (vsFTPd 3.0.3)
Name (10.129.153.39:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||43362|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp> page allowed.userlist
aron
pwnmeow
egotisticalsw
admin
ftp> page allowed.userlist.passwd
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```

Logon to website using the admin credentials of rKXM59ESxesUFHAd

![Pasted image 20221006053800.png](HTB-Crocodile/Image1.png)

Answer: c7110277ac44d78b6a9fff2232434d16