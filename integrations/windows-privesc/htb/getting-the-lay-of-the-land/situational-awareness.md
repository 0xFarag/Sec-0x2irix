# Situational Awareness

## Situational Awareness

## **Network Information**

**"Dual-homed" hosts:** device connected with more than one network

### Display Interface(s), IP Address(es), DNS Information

```powershell
ipconfig /all
```

> which display name of device and workgroup or domain info

### Display ARP Table

which display all devices were communicated with this device

```powershell
arp -a
```

### Display Routing Table

```powershell
route print
```

***

## **Enumerating Protections**

discover all protection like **EDR** or **Application Whitelisting (AppLocker)**

### **Check Windows Defender Status**

We can use the GetAppLockerPolicy cmdlet to enumerate the local, effective (enforced), and domain AppLocker policies.&#x20;

```powershell
Get-MpComputerStatus
```

![image.png](<../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png>)

### **List AppLocker Rules**

```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

<figure><img src="../../../../.gitbook/assets/image 1 (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **Test AppLocker Policy**

```powershell
PS C:\irix> Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone

FilePath                    PolicyDecision MatchingRule
--------                    -------------- ------------
C:\Windows\System32\cmd.exe         Denied c:\windows\system32\cmd.exe
```
