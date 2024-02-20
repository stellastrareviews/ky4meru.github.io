---
layout: default
title: Fuzzing
parent: Web Applications
nav_order: 3
permalink: /webapps/fuzzing/
---

# Fuzzing
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

```bash
# Simple usage.
gobuster dir -u http://$target -w $wordlist

# For a complete scan.
gobuster dir -u http://$target/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -x $type
```