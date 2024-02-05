---
layout: default
title: Cracking
nav_order: 5
has_children: true
permalink: /cracking/
has_toc: false
---

# Cracking
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Let's crack...

* [KDBX](/cracking/kdbx/)
* [SSH Private Key](/cracking/ssh/)

## Useful links

* [Kwprocessor](https://github.com/hashcat/kwprocessor) to generate key-walk passwords.
* [Rule-based Attack](https://hashcat.net/wiki/doku.php?id=rule_based_attack) to create your own cracking rules.
* [OneRuleToRuleThemStill](https://github.com/stealthsploit/OneRuleToRuleThemStill) of the best rule.

## Tips and Tricks

```bash
# Determine type of hash for '-m' hashcat option.
echo "$hash" | hashid

# Crack with hashcat using masks (hashcat --help)
hashcat $hash ?u?l?H?h?a?s?b?d
hashcat $hash file.hcmask
```