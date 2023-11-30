---
layout: default
title: Setuid Capabilities
parent: Linux
nav_order: 1
permalink: /linux/setuid/
---

# Setuid Capabilities
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Low privileged access to the Linux target.

## Exploit

First, search for binaries that have *setuid* capabilities enabled with *+ep* flag (effective and permitted).

```bash
getcap -r / 2>/dev/null | grep setuid
```

Once you find a suitable binary, you can either try to exploit it by your way, or check on [GTFOBins](https://gtfobins.github.io/).