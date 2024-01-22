---
layout: default
title: Linux Enumeration
parent: Linux
nav_order: 3
permalink: /linux/enumeration/
---

# Linux Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Automated tools

```bash
./unix-privesc-check standard > output.txt
./lynis audit system
./linpeas.sh
```

## Manual enumeration

Don't forget to take a loot at [Antivirus Enumeration](/antivirus/enumeration/) page to list active antivirus and EDRs on the Linux host.

```bash
# Get user information
whoami
id
cat /etc/passwd
sudo -l

# Get system information
hostname
cat /etc/issue
ps aux
ip a
routel
netstat -tulpn | grep LISTEN
cat /etc/iptables/rules.v4
ls -lah /etc/cron*
[sudo] crontab -l
dpkg -l
find / -writable -type d 2>/dev/null
cat /etc/fstab
lsblk
lsmod
/sbin/modinfo libata
find / -perm -u=s -type f 2>/dev/null

# Interesting files
env
cat /home/$USERNAME/.bashrc
cat /home/$USERNAME/.ssh/id_rsa

# Processes
ps aux
watch -n 1 "ps -aux | grep pass"
sudo tcpdump -i lo -A | grep "pass"
```