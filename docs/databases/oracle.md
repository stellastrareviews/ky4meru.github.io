---
layout: default
title: Oracle
parent: Databases
nav_order: 1
permalink: /databases/oracle/
---

# Oracle
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Network route to the Oracle TNS Listener (port 1521 by default).

## Exploit

```bash
# Enumerate vulnerabilities on Oracle database.
./odat all -s $target -p $port
```

## Useful links

* [Oracle Database Attacking Tool](https://github.com/quentinhardy/odat)

## Recommendations

- [ ] TODO: List recommendations here.