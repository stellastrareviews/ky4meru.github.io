---
layout: default
title: Printer Abuse
parent: Active Directory
nav_order: 13
permalink: /ad/printer/
---

# Printer Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Vulnerability

Printers are usually present within companies. Sometimes, these printers are joined to the domain to authenticate users with their company's account.

To authenticate users, printers generally use LDAP requests to the domain. Therefore, the purpose of this attack is to gain access to the configuration web page of the printer to relay authentication requests to your environement instead to the domain.

## Prerequisites

- Target must be a domain joined printer using LDAP authentication.

## Exploit

First, you need to find printers on the network. To do so, start by scanning web ports with `nmap` or `masscan` to find printer's web administration pages. Then, you can use `aquatone` to facilitate your research.

```bash
# Scan open web ports on the network.
sudo masscan $RANGE -p80,443,8000,8008,8080,8443 -oX out.xml

# Put the result in aquatone to screenshot web pages.
cat out.xml | aquatone -out ./aquatone -nmap

# Look at the results.
feh ./aquatone/screenshots
```

If you find printers, they might be protected with authentication (it is not always the case). Do not panic, default credentials are often in use. Below is a summary of default credentials depending on the printer brand and model. You can also try to exploit RCE on the printer's web page, they are often vulnerable.

Once you are authenticated on the printer as an administrator, search for the LDAP configuration page. If the printer authenticates users on the domain, you should see a field somewhere that specifies the destination IP or DNS.

Change this field with your local IP and start a `responder` to authentication requests.

```bash
# To get your IPv4 and your interface.
ip a

# Then start responder.
sudo responder -wrfbudPF -I $INTERFACE
```

That's all, just wait for someone to use the printer you just compromise and you will get your initial foothold.

## Recommendations

- [ ] Do not configure LDAP authentication on printers.
- [ ] If you do, enforce a strong administrator password.