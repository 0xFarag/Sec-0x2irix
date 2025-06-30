# Using BloodHound

**Remote BloodHound -Linux**

```bash
bloodhound-python -u <UserName> -p <Password> -ns <Domain Controller's Ip> -d <Domain> -c All
```

**On Site BloodHound - Windows**

```powershell
#Using exe ingestor
.\\SharpHound.exe --CollectionMethod All
.\\SharpHound.exe --CollectionMethod All --LdapUsername <UserName> --LdapPassword <Password> --domain <Domain> --domaincontroller <Domain Controller's Ip> --OutputDirectory <PathToFile>

#Using PowerShell module ingestor
. .\\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All --LdapUsername <UserName> --LdapPassword <Password> --OutputDirectory <PathToFile>
```
