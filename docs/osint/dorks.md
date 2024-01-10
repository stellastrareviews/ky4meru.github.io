---
layout: default
title: Google Dorks
parent: Open Source Intelligence
nav_order: 1
permalink: /osint/dorks/
---

# Google Dorks
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How to

```bash
# Limit search results to the DOMAIN
site:<$DOMAIN>

# Limit search results to specific file TYPE (ex: filetype:txt)
filetype:<$TYPE>

# Limit search results to specific extension (ex: ext:py, ext:xml, etc.)
ext:<$EXTENSION>

# Limit search results to pages that contain "MULTIPLE WORDS" in the title
intitle:"$MULTIPLE_WORDS"

# You can exclude with "-" as below
# Search pages on fruits.com containing "Love Fruits" in the title, excluding xml pages
site:cars.com -filetype:xml intitle:"Love cars"
```

## Useful links

* [DorkSearch](https://dorksearch.com/)
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database)