---
layout: default
title: Fuzzing
parent: Web Applications
nav_order: 1
permalink: /webapps/fuzzing/
---

# Fuzzing
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO

## Exploit

```bash
gobuster dir -u http://$TARGET -w $WORDLIST -o gobuster.out -x txt,pdf,config
```