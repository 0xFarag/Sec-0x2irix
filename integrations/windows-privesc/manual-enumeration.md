# Manual Enumeration

### <mark style="color:red;">System Enumeration:</mark>

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

***

### <mark style="color:red;">User Enumeration:</mark>

**Display Privilege for current user**

```bash
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

***

### <mark style="color:red;">Network Enumeration</mark>

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

**Show active network connections with PIDs**

```bash
netstat -ano	
```

***

### <mark style="color:red;">Password Hunting</mark>

**Search for "password" in common config files**

```bash
findstr /si password *.txt *.ini *.config	
```

**Recursively search all files for password patterns**

```bash
findstr /spin "password" *.*	
```

**Look for plaintext passwords in Windows setup files**

```bash
type %WINDIR%\\Panther\\Unattend\\Unattended.xml	
```

***

### <mark style="color:red;">AV Enumeration</mark>

**Check status of Windows Defender**

```bash
sc query windefend	
```

**List all services, including antivirus software**

```bash
sc queryex type= service	
```
