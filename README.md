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
## SMBClient Scan
Listing the Smbshares 

<img width="759" height="362" alt="Image" src="https://github.com/user-attachments/assets/c326302c-c5be-43bf-85d2-ebb71b1a6818" />

 Go to the Anonymous Share 

 <img width="689" height="329" alt="Image" src="https://github.com/user-attachments/assets/edb032be-5402-4829-9576-d5666dcc45bb" />

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
