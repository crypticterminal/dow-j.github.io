---
title: OverTheWire Bandit Wargame Walkthrough
date: '2017-12-16 13:28:00 +0000'
author: dow-j
categories:
  - walkthroughs
published: true
---

Lately I've been having a lot of fun with wargames, particularly those done by [OverTheWire](http://overthewire.org/wargames/). I've completed a few of their servers at the time of writing this, and what follows is the solution to their "Bandit" wargame. This server is intended for those new to the site, but you should have some _basic_ linux familiarity before jumping in.

I **highly** recommending attempting the levels yourself first before referring to my solutions.

## Level 0

The goal for this level is for you to log into the game using SSH. The host to which you need to connect is **bandit.labs.overthewire.org**, on port 2220. The username is **bandit0** and the password is **bandit0**. Once logged in, go to the Level 1 page to find out how to beat Level 1.

```
$ ssh bandit0@bandit.labs.overthewire.org -p 2220
```

## Level 0 -> 1

The password for the next level is stored in a file called **readme** located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

```
$ ls
readme
$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

## Level 1 -> 2

The password for the next level is stored in a file called **-** located in the home directory

```
$ ls
-
$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

## Level 2 -> 3

The password for the next level is stored in a file called **spaces in this filename** located in the home directory

```
$ ls
spaces in this filename
$ cat ./spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

## Level 3 -> 4

The password for the next level is stored in a hidden file in the **inhere** directory.

```
$ ls
inhere
$ cd inhere
$ ls -a
. .. .hidden
$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

## Level 4 -> 5

The password for the next level is stored in the only human-readable file in the **inhere** directory. Tip: if your terminal is messed up, try the “reset” command.

```
$ ls
inhere
$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

## Level 5 -> 6

The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:

- human-readable
- 1033 bytes in size
- not executable

```
$ ls
inhere
$ cd inhere
$ find . -type f -size 1033c ! -executable
./maybehere07/.file2
$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

## Level 6 -> 7

The password for the next level is stored **somewhere on the server** and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

```
$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

## Level 7 -> 8

The password for the next level is stored in the file data.txt next to the word millionth

```
$ ls
data.txt
$ grep "millionth" ./data.txt
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

## Level 8 -> 9

The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once

```
$ ls
data.txt
$ cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

## Level 9 -> 10

The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, beginning with several ‘=’ characters.

```
$ ls
data.txt
$ strings data.txt | grep "="
========== theOkM
L=8@
=hrV`
========== password
========== is
H)=QU
>]".x=
{=u/,i_
{=jh
=GUl
e=y:
4H5=
)========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
=/wW
~BX=
```

## Level 10 -> 11

The password for the next level is stored in the file **data.txt**, which contains base64 encoded data

```
$ ls
data.txt
$ cat data.txt | base64 --decode
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

## Level 11 -> 12

The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

```
$ ls
data.txt
$ cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

## Level 12 -> 13

The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

```
$ ls
data.txt
$ mkdir /tmp/test_123
$ cd /tmp/test_123
$ cp ~/data.txt /.
$ file data.txt
data.txt: ASCII text
$ xxd -r data.txt > data_xxd_reverse
$ file data_xxd_reverse
data_xxd_reverse: gzip compressed data, was "data2.bin", last modified: Mon Nov 13 14:58:07 2017, max compression, from Unix
$ zcat data_xxd_reverse > data_zcat
$ file data_zcat
data_zcat: bzip2 compressed data, block size = 900k
$ bzip2 -d data_zcat
bzip2: Can't guess original name for data_zcat -- using data_zcat.out
$ file data_zcat.out
data_zcat.out: gzip compressed data, was "data4.bin", last modified: Mon Nov 13 14:58:07 2017, max compression, from Unix
$ zcat data_zcat.out > data_zcat_2
$ file data_zcat_2
data_zcat_2: POSIX tar archive (GNU)
$ tar xvf data_zcat_2
$ file data5.bin
data5.bin: POSIX tar archive (GNU)
$ tar xvf data5.bin
data6.bin
$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
$ bzip2 -d data6.bin
bzip2: Can't guess original name for data6.bin -- using data6.bin.out
$ file data6.bin.out
data6.bin.out: POSIX tar archive (GNU)
$ tar xvf data6.bin.out
data8.bin
$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Mon Nov 13 14:58:07 2017, max compression, from Unix
$ zcat data8.bin > data8_zcat
$ file data8_zcat
data8_zcat: ASCII text
$ cat data8_zcat
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

## Level 13 -> 14

The password for the next level is stored in **/etc/bandit_pass/bandit14** and can only be read by user **bandit14**. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. **Note: localhost** is a hostname that refers to the machine you are working on

```
$ ls
sshkey.private
$ ssh -i sshkey.private bandit14@localhost
$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```

## Level 14 -> 15

The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

```
$ telnet localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

## Level 15 -> 16

The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using SSL encryption.

<div class="note info">
  <h5>Getting “HEARTBEATING” and “Read R BLOCK”?</h5> 
  <p>Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…</p>
</div>

```
$ openssl s_client -connect localhost:30001 -ign_eof
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
```

## Level 16 -> 17

The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

```
$ nmap -sV -A -p 31000-32000 localhost
Starting Nmap 7.01 ( https://nmap.org ) at 2017-12-19 02:40 CET
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00010s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
| ssl-cert: Subject: commonName=bandit
| Not valid before: 2017-11-11T20:05:12
|_Not valid after:  2027-11-09T20:05:12
|_ssl-date: TLS randomness does not represent time
31691/tcp open  echo
31790/tcp open  ssl/unknown
| ssl-cert: Subject: commonName=bandit
| Not valid before: 2017-11-11T20:05:12
|_Not valid after:  2027-11-09T20:05:12
|_ssl-date: TLS randomness does not represent time
31960/tcp open  echo
$ openssl s_client -connect 127.0.0.1:31790 -quiet
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
$ mkdir /tmp/test123456
$ cd /tmp/test123456
$ vi sshkey.private
(paste RSA key from openssl earlier)
$ chmod 600 sshkey.private
$ ssh -i ./sshkey.private bandit17@localhost
```

## Level 17 -> 18

There are 2 files in the homedirectory: **passwords.old and passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old and passwords.new**

<div class="note info">
<p>If you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19</p>
</div>

```
$ ls
passwords.new  passwords.old
$ diff passwords.new passwords.old
< kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
---
> BS8bqB1kqkinKJjuxL6k072Qq9NRwQpR
```

## Level 18 -> 19

The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.
```
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames
bandit18@bandit.labs.overthewire.org's password: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

## Level 19 -> 20

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

```
$ ls
bandit20-do
$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id
$ ./bandit20-do id
uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)
$ ./bandit20-do whoami
bandit20
$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

## Level 20 -> 21

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

<div class="note info">
  <h5>Changes to the infrastructure made this level more difficult.</h5> 
  <p>You will need to figure out a way to launch multiple commands in the same Docker instance.</p>
</div>

<div class="note">
  <p>Try connecting to your own network daemon to see if it works as you think</p>
</div>

```
$ ls
suconnect
$ .suconnect
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.
$ nc -l 5666
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
<Open a new terminal>
$ ./suconnect 5666
<In the previous terminal>
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```

## Level 21 -> 22

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

```
$ cd /etc/cron.d
$ ls -l
-rw-r--r-- 1 root root 120 Nov 13 15:58 cronjob_bandit22
-rw-r--r-- 1 root root 122 Nov 13 15:58 cronjob_bandit23
-rw-r--r-- 1 root root 120 Nov 13 15:58 cronjob_bandit24
-rw-r--r-- 1 root root 190 Oct 31 13:21 popularity-contest
$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
$ cat /usr/bin/cronjob_bandit22.sh
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

## Level 22 -> 23

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

<div class="note info">
  <h5>Looking at shell scripts written by other people is a very useful skill.</h5> 
  <p>The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.</p>
</div>

```
$ cd /etc/cron.d
$ ls -l
-rw-r--r-- 1 root root 120 Nov 13 15:58 cronjob_bandit22
-rw-r--r-- 1 root root 122 Nov 13 15:58 cronjob_bandit23
-rw-r--r-- 1 root root 120 Nov 13 15:58 cronjob_bandit24
-rw-r--r-- 1 root root 190 Oct 31 13:21 popularity-contest
$ cat cronjob_bandit23 
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

## Level 23 -> 24

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

<div class="note">
  <h5>This level requires you to create your own first shell-script.</h5> 
  <p>This is a very big step and you should be proud of yourself when you beat this level!</p>
</div>

<div class="note warning">
  <p>Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…</p>
</div>

```
$ cd /etc/cron.d
$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        timeout -s 9 60 ./$i
        rm -f ./$i
    fi
done
$ mkdir /tmp/test1234
$ cd /tmp/test1234
$ nano bandit24.sh
$ cat /etc/bandit_pass/bandit24 >> /tmp/test1234/bandit24
$ chmod 777 bandit24.sh
$ chmod 777 /tmp/test1234
$ cp bandit24.sh /var/spool/bandit24/
$ cat /tmp/test1234/bandit24
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

## Level 24 -> 25

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

```
$ nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
$ mkdir /tmp/test24
$ cd /tmp/test24
$ nano brute-force.sh
for i in {1..10000}

do
        echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i" >> ./24.txt
done
$ chmod 700 ./brute-force.sh
$ cat 24.txt | nc localhost 30002
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
```

## Level 25 -> 26

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not **/bin/bash**, but something else. Find out what it is, how it works and how to break out of it..

```
$ ls
bandit26.sshkey
$ ssh bandit26@localhost -i bandit26.sshkey
  _                     _ _ _   ___   __  
 | |                   | (_) | |__ \ / /  
 | |__   __ _ _ __   __| |_| |_   ) / /_  
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \ 
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/ 
Connection to localhost closed.
$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
<make terminal vertically small to trigger more>
$ ssh bandit26@localhost -i bandit26.sshkey
v (escape shell and enter visual mode
:set shell=/bin/bash
:shell
$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```
