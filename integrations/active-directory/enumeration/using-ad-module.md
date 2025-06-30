# Using AD Module

**Get Current Domain:**

```powershell
Get-ADDomain
```

**Enum Other Domains:**

```powershell
Get-ADDomain -Identity <Domain>
```

**Get Domain SID:**

```powershell
Get-DomainSID
```

**Get Domain Controlers:**

```powershell
Get-ADDomainController
Get-ADDomainController -Identity <DomainName>
```

**Enumerate Domain Users:**

```powershell
Get-ADUser -Filter * -Identity <user> -Properties *

#Get a specific "string" on a user's attribute
Get-ADUser -Filter 'Description -like "*wtver*"' -Properties Description | select Name, Description
```

**Enum Domain Computers:**

```powershell
Get-ADComputer -Filter * -Properties *
Get-ADGroup -Filter *
```

**Enum Domain Trust:**

```powershell
Get-ADTrust -Filter *
Get-ADTrust -Identity <DomainName>
```

**Enum Forest Trust:**

```powershell
Get-ADForest
Get-ADForest -Identity <ForestName>

#Domains of Forest Enumeration
(Get-ADForest).Domains
```

**Enum Local AppLocker Effective Policy:**

```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```
