# Methodology
>1. Enumerate links to remote servers.
>2. Abuse links to get foothold on remote servers.

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerUpSQL
```powershell
Get-SQLServerLink -Instance us-mssql.us.techcorp.local -Verbose
```