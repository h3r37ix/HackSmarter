# Samurai (Easy)

![banner](img/banner.png)

## Overview
**Target Name:** Samurai

**Difficulty:** Easy

**OS:** Ubuntu

**Objective:** As part of a penetration test, your team identified an interesting web server. Your task is to enumerate the target, establish an initial foothold, and escalate privileges to root.

## Enumeration:
### Port Scanning
Ran Rustscan then piped results to Nmap
```
rustscan -a http://10.1.104.188 -- -A
```
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c3:5a:83:50:80:9a:61:37:05:b7:45:96:cb:ab:1d:1e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDnWIbBLcbSbZZmw8nDh5DOA9ecneGMU8Ff1Rm8Frp71DcloANVhYkmErZ3+o839XNGO+k2tmXeNcwJ8jICj06M=
|   256 6b:15:12:60:1b:21:d1:bf:7e:b8:c0:e8:d7:7e:7b:6b (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP9JIv57fNRXYSBb4BDtI+WNZG/hfJuGHaaMLL7Iu9PG
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Samurai
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 3E18B73692FF5A74F54EFFB2E047C8CB
```
### SSH Login Test
SSH configured for password login, advise to use keys.
![sshcheck](img/sshcheck.png)

### Directory Enumeration:
Ran Gobuster on TLD to enumerate subdirectories.

```
─$ gobuster dir -u http://10.1.104.188 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt  --exclude-length 3185 
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.1.104.188
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] Exclude Length:          3185
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
images               (Status: 301) [Size: 313] [--> http://10.1.104.188/images/]
templates            (Status: 301) [Size: 316] [--> http://10.1.104.188/templates/]
media                (Status: 301) [Size: 312] [--> http://10.1.104.188/media/]
modules              (Status: 301) [Size: 314] [--> http://10.1.104.188/modules/]
assets               (Status: 301) [Size: 313] [--> http://10.1.104.188/assets/]
plugins              (Status: 301) [Size: 314] [--> http://10.1.104.188/plugins/]
includes             (Status: 301) [Size: 315] [--> http://10.1.104.188/includes/]
language             (Status: 301) [Size: 315] [--> http://10.1.104.188/language/]
components           (Status: 301) [Size: 317] [--> http://10.1.104.188/components/]
api                  (Status: 301) [Size: 310] [--> http://10.1.104.188/api/]
cache                (Status: 301) [Size: 312] [--> http://10.1.104.188/cache/]
libraries            (Status: 403) [Size: 277]
tmp                  (Status: 301) [Size: 310] [--> http://10.1.104.188/tmp/]
layouts              (Status: 301) [Size: 314] [--> http://10.1.104.188/layouts/]
administrator        (Status: 301) [Size: 320] [--> http://10.1.104.188/administrator/]
cli                  (Status: 301) [Size: 310] [--> http://10.1.104.188/cli/]
Progress: 87662 / 87662 (100.00%)
===============================================================
```

Oh look an administrator directory. We can see that brings us to a Joomla login page. 

### Joomla Version Check
A misconfiguration allows us to check the Joomla version by navigating to an xml file.

```
http://10.1.104.188/administrator/manifests/files/joomla.xml
```

![joomlaversion](img/joomlaversion.png)

```
Version: 4.2.5
```


