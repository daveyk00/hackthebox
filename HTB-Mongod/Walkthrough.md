Connect the VPN, spawn the VM. <br>
10.129.143.221


###  Task 1

How many TCP ports are open on the machine? 
```
# nmap -sS 10.129.143.221 -p- -n -Pn
```

And the results:
```
Nmap scan report for 10.129.143.221
Host is up (0.25s latency).
Not shown: 65533 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
27017/tcp open  mongod

Nmap done: 1 IP address (1 host up) scanned in 1213.84 seconds

```

Answer: 2


###  Task 2

Which service is running on port 27017 of the remote host? 


We didn't do service identification on the previous nmap, so lets run that now:
```
# nmap -sS 10.129.143.221 -sV -p 27017 -n -Pn
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-02 05:27 EDT
Nmap scan report for 10.129.143.221
Host is up (0.23s latency).

PORT      STATE SERVICE VERSION
27017/tcp open  mongodb MongoDB 3.6.8

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.11 seconds
```

Answer: MpongoDB 3.6.8

###  Task 3

What type of database is MongoDB? (Choose: SQL or NoSQL) 

Answer: NoSQL

###  Task 4

What is the command name for the Mongo shell that is installed with the mongodb-clients package? 

Answer: mongo

Unfortunately when I tried to run mongo from the command line in Kali it failed with:
```
└─# mongo        
zsh: illegal hardware instruction  mongo
```

I used google to get the mongo command parameters, and mongosh for the remainder

###  Task 5

What is the command used for listing all the databases present on the MongoDB server? (No need to include a trailing ;) 

Connect to the database:
```
└─# mongosh "mongodb://10.129.143.221:27017" 
```

```
test> help
```

```

  Shell Help:

    use                                        Set current database
    show                                       'show databases'/'show dbs': Print a list of all available databases.
                                               'show collections'/'show tables': Print a list of all collections for current database.
                                               'show profile': Prints system.profile information.
                                               'show users': Print a list of all users for current database.
                                               'show roles': Print a list of all roles for current database.
                                               'show log <type>': log for current connection, if type is not set uses 'global'
                                               'show logs': Print all logs.

    exit                                       Quit the MongoDB shell with exit/exit()/.exit
    quit                                       Quit the MongoDB shell with quit/quit()
    Mongo                                      Create a new connection and return the Mongo object. Usage: new Mongo(URI, options [optional])
    connect                                    Create a new connection and return the Database object. Usage: connect(URI, username [optional], password [optional])
    it                                         result of the last line evaluated; use to further iterate
    version                                    Shell version
    load                                       Loads and runs a JavaScript file into the current shell environment
    enableTelemetry                            Enables collection of anonymous usage data to improve the mongosh CLI
    disableTelemetry                           Disables collection of anonymous usage data to improve the mongosh CLI
    passwordPrompt                             Prompts the user for a password
    sleep                                      Sleep for the specified number of milliseconds
    print                                      Prints the contents of an object to the output
    printjson                                  Alias for print()
    cls                                        Clears the screen like console.clear()
    isInteractive                              Returns whether the shell will enter or has entered interactive mode

  For more information on usage: https://docs.mongodb.com/manual/reference/method

```

Answer:  show dbs
```
test> show dbs
admin                  32.00 KiB
config                 72.00 KiB
local                  72.00 KiB
sensitive_information  32.00 KiB
users                  32.00 KiB
test> 
(To exit, press Ctrl+C again or Ctrl+D or type .exit)
```


###  Task 6

What is the command used for listing out the collections in a database? (No need to include a trailing ;) 

```
test> use admin
switched to db admin
admin> show collections
system.version
admin> 
```

Answer: show collections

###  Task 7

What is the command used for dumping the content of all the documents within the collection named flag in a format that is easy to read? 

```
admin> use sensitive_information
switched to db sensitive_information
sensitive_information> db.flag.find().<tab><tab>
```

Gives the list of commands to use with find().

```
sensitive_information> db.flag.find().
db.flag.find().allowPartialResults  db.flag.find().collation            db.flag.find().comment              db.flag.find().hasNext              db.flag.find().hint                 db.flag.find().limit
db.flag.find().max                  db.flag.find().maxAwaitTimeMS       db.flag.find().min                  db.flag.find().next                 db.flag.find().noCursorTimeout      db.flag.find().oplogReplay
db.flag.find().readPref             db.flag.find().returnKey            db.flag.find().size                 db.flag.find().tailable             db.flag.find().showRecordId         db.flag.find().readConcern
db.flag.find().batchSize            db.flag.find().close                db.flag.find().forEach              db.flag.find().tryNext              db.flag.find().isClosed             db.flag.find().isExhausted
db.flag.find().itcount              db.flag.find().toArray              db.flag.find().pretty               db.flag.find().map                  db.flag.find().maxTimeMS            db.flag.find().projection
db.flag.find().skip                 db.flag.find().sort                 db.flag.find().objsLeftInBatch      db.flag.find().explain
```

Answer: db.flag.find().pretty()

###  Submit Flag

Submit root flag 
Answer: 1b6e6fb359e7c40241b6d431427ba6ea

