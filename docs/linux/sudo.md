---
layout: default
title: Sudo Abuse
parent: Linux
nav_order: 6
permalink: /linux/sudo/
---

# Sudo Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .important }
> Performing following exploit, you could be blocked by AppArmor (Linux kernel module). To check if the binary is in a protected mode, run `aa-status`.

## Prerequisites

- Low privileged access to the Linux target.

## Exploit

Once you are authenticated with a unprivileged user on Linux, you can list his sudo capabilities.

```bash
sudo -l

# You are interested in last lines listing commands the user can run in sudo.
# User joe may run the following commands on debian-privesc:
    # (ALL) (ALL) /usr/bin/crontab -l, /usr/sbin/tcpdump, /usr/bin/apt-get
```

Now, you can try to exploit these binaries with [GTFOBins](https://gtfobins.github.io/).