# Windows Defender

{% hint style="danger" %}
**Note:** If Tamper protection is enabled you will not be able to turn off Defender by CMD or PowerShell. You can however, still **create an exclusion.**
{% endhint %}

**Disable real time monitoring**

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
```

**Disable scanning for downloaded files (more silent and preferred)**

```powershell
Set-MpPreference -DisableIOAVProtection $true
```

**Create an exclusion**

```powershell
Add-MpPreference -ExclusionPath "C:\Windows\Temp"
```
