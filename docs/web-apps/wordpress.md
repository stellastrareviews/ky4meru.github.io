---
layout: default
title: WordPress
parent: Web Applications
nav_order: 3
permalink: /webapps/wordpress/
---

# WordPress
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
wpscan --url http://$TARGET --enumerate p --plugins-detection aggressive -o wpscan.out
```