# Methodology
>1. Enumerate trusts.
>2. Enumerate PAM trusts (ForestTransitive true and SIDFIlteringForestAware is false).
>3. Enumerate `Shadow Principals`.
>4. Abuse PAM trust.

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## AD Module
https://github.com/samratashok/ADModule

**Enumerate if PAM trust is enabled:**
```powershell
Get-ADTrust -Filter {(ForestTransitive - eq $True) -and (SIDFilteringQuarantined -eq $False)}
```

**Check what users are member os shadow principals:**
```powershell
Get-ADObject -SearchBase ("CN=Shadow Principal Configuration,CN=Services," + (GetADRootDSE).configurationNamingContext) -Filter * -Properties * | select Name,member,msDS-ShadowPrincipalSid | fl
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).