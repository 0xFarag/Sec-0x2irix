# Using PowerView

**Get Current Domain**

```powershell
Get-Domain
```

**Enumerate Other Domains**

```powershell
Get-Domain -Domain <DomainName>
```

**Get Domain SID**

```powershell
Get-DomainSID
```

**Get Domain Policy**

```powershell
Get-DomainPolicy
# like max ticket age , miminum, maximum pass

#Will show us the policy configurations of the Domain about system access or kerberos
Get-DomainPolicy | Select-Object -ExpandProperty SystemAccess
Get-DomainPolicy | Select-Object -ExpandProperty KerberosPolicy
```

**Get Domain Controllers**

```powershell
# information like ip, os Version
Get-DomainController
Get-DomainController -Domain <DomainName>
```

**Enumerate Domain Users:**

```powershell
#Save all Domain Users to a file
Get-DomainUser | Out-File -FilePath .\\DomainUsers.txt

#Will return specific properties of a specific user
Get-DomainUser -Identity [username] -Properties DisplayName, MemberOf | Format-List

#Enumerate user logged on a machine
Get-NetLoggedon -ComputerName <ComputerName>

#Enumerate Session Information for a machine
Get-NetSession -ComputerName <ComputerName>

#Enumerate domain machines of the current/specified domain where specific users are logged into
Find-DomainUserLocation -Domain <DomainName> | Select-Object UserName, SessionFromName
```

**Enum Domain Computers:**

```powershell
# Eumerate all computers with vrersion,name.DNS
Get-DomainComputer -Properties OperatingSystem, Name, DnsHostName |  Select-Object Name, DnsHostName, OperatingSystem |  Sort-Object -Property DnsHostName
#Enumerate Live machines
Get-DomainComputer -Ping -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName
```

**Enum Groups and Group Members:**

```powershell
#Save all Domain Groups to a file:
Get-DomainGroup | Out-File -FilePath .\\DomainGroup.txt

#Return members of Specific Group (eg. Domain Admins & Enterprise Admins)
Get-DomainGroup -Identity '<GroupName>' | Select-Object -ExpandProperty Member
Get-DomainGroupMember -Identity '<GroupName>' | Select-Object MemberDistinguishedName

#Enumerate the local groups on the local (or remote) machine. Requires local admin rights on the remote machine
Get-NetLocalGroup | Select-Object GroupName

#Enumerates members of a specific local group on the local (or remote) machine. Also requires local admin rights on the remote machine
Get-NetLocalGroupMember -GroupName Administrators | Select-Object MemberName, IsGroup, IsDomain

# 
#Return all GPOs in a domain that modify local group memberships through Restricted Groups or Group Policy Preferences
Get-DomainGPOLocalGroup | Select-Object GPODisplayName, GroupName
```

**Enumerate Shares:**

```powershell
# List all shared folders across domain machines
Find-DomainShare

# List shares you can access
Find-DomainShare -CheckShareAccess

# Search for interesting files in shares (e.g. containing "passwords")
Find-InterestingDomainShareFile -Include *passwords*
```

**Enum Group Policies:**

```powershell
# List all GPOs in the domain (sorted by name) => AutoLogon, Deny CMD Access
Get-DomainGPO -Properties DisplayName | Sort-Object -Property DisplayName

# List GPOs applied to a specific computer
Get-DomainGPO -ComputerIdentity <ComputerName> -Properties DisplayName | Sort-Object -Property DisplayName

# Enumerate users/groups added to the local Administrators group by GPO
Get-DomainGPOComputerLocalGroupMapping -ComputerName <ComputerName>
```

**Enum OUs:**

```powershell
Get-DomainOU -Properties Name | Sort-Object -Property Name
```

**Enum ACLs:**

```powershell
# Returns the ACLs associated with the specified account
Get-DomaiObjectAcl -Identity <AccountName> -ResolveGUIDs

#Search for interesting ACEs
Find-InterestingDomainAcl -ResolveGUIDs

#Check the ACLs associated with a specified path (e.g smb share)
Get-PathAcl -Path "\\\\Path\\Of\\A\\Share"
```

**Enum Domain Trust:**

```powershell
Get-DomainTrust
Get-DomainTrust -Domain <DomainName>

#Enumerate all trusts for the current domain and then enumerates all trusts for each domain it finds
Get-DomainTrustMapping
```

**Enum Forest Trust:**

```powershell
Get-ForestDomain
Get-ForestDomain -Forest <ForestName>

#Map the Trust of the Forest
Get-ForestTrust
Get-ForestTrust -Forest <ForestName>
```

**User Hunting:**

```powershell
#Finds all machines on the current domain where the current user has local admin access
Find-LocalAdminAccess -Verbose

#Find local admins on all machines of the domain
Find-DomainLocalGroupMember -Verbose

#Find computers were a Domain Admin OR a specified user has a session
Find-DomainUserLocation | Select-Object UserName, SessionFromName

#Confirming admin access
Test-AdminAccess
```
