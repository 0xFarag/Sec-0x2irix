# ACL Enumeration

### **Enumerating ACLs with PowerView**

we start to target user to save time

*   **first define SID for this user**

    ```powershell
    Import-Module .\PowerView.ps1
    $sid = Convert-NameToSid wley
    ```
*   **dig into and see if this user has any interesting ACL - GUID Flags**

    ```powershell
    Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}
    ```

    ![](<../../../../.gitbook/assets/2025 06 26_20_48_56 Hack_The_Box_ _Academy_ _Brave.png>)

    search for GUID in google we found this user have permission to force change the other user's password

    or can use

    *   **Performing a Reverse Search & Mapping to a GUID Value**

        ```bash
        $guid= "00299570-246d-11d0-a768-00aa006e0529"
        Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl
        ```

        ![](<../../../../.gitbook/assets/2025 06 26_20_56_28 Hack_The_Box_ _Academy_ _Brave.png>)
*   **Search for interesting ACEs**

    ```powershell
    Find-InterestingDomainAcl -ResolveGUIDs
    ```
*   **ResolveGUIDs Flag -** we can start by this in the first step

    ```powershell
    Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 

    # Ex
    Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs -
    ```

    ![](<../../../../.gitbook/assets/2025 06 26_20_59_29 Hack_The_Box_ _Academy_ _Brave.png>)
*   **Enumeration of Rights Using damundsen**

    ```powershell
    $sid2 = Convert-NameToSid damundsen
    Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2} -Verbose
    ```

    ![image.png](<../../../../.gitbook/assets/image (46).png>)

    &#x20;We found **damundsen user** has `GenericWrite` privileges over the `Help Desk Level 1` group This means, among other things, that we can add any user (or ourselves) to this group and inherit any rights that this group&#x20;
*   **Investigating this group is nested into any other groups**

    ```powershell
    Get-DomainGroup -Identity "Help Desk Level 1" | select memberof
    ```

    <figure><img src="../../../../.gitbook/assets/image 1 (26).png" alt=""><figcaption></figcaption></figure>

    > **that’s mean two groups are nasted**
*   **Investigating the nested Group**

    ```powershell
    $itgroupsid = Convert-NameToSid "Information Technology"
    Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose
    ```

    <figure><img src="../../../../.gitbook/assets/image 2 (15).png" alt=""><figcaption></figcaption></figure>

    Finally, let's see if the `adunn` user has any type of interesting access that we may be able to leverage to get closer to our goal.
*   **Looking for Interesting Access**

    ```powershell
    $adunnsid = Convert-NameToSid adunn 
    Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $adunnsid} -Verbose
    ```

    <figure><img src="../../../../.gitbook/assets/image 3 (11).png" alt=""><figcaption></figcaption></figure>

    &#x20;`adunn` user has `DS-Replication-Get-Changes` and `DS-Replication-Get-Changes-In-Filtered-Set` rights over the domain object. This means that this user can be leveraged to perform a DCSync attack.&#x20;

***

### **Enumerating ACLs with other way**

*   **create list for all domain users -** we can use it with powerview too

    ```powershell
    Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt
    ```
*   **Get ACL for each user using for loop**

    ```powershell
    foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}
    ```

    <figure><img src="../../../../.gitbook/assets/image 4 (9).png" alt=""><figcaption></figcaption></figure>

***

### BloodHound

we use the data collected from `SharpHound ingestor`

&#x20;we can set the `wley` user as our starting node, select the `Node Info` tab and scroll down to `Outbound Control Rights`. This option will show us objects we have control over directly, via group membership, and the number of objects that our user could lead to us controlling via ACL attack paths under `Transitive Object Control`. If we click on the `1` next to `First Degree Object Control`, we see the first set of rights that we enumerated, `ForceChangePassword` over the `damundsen` user.

<figure><img src="../../../../.gitbook/assets/image 5 (7).png" alt=""><figcaption></figcaption></figure>

If we right-click on the line between the two objects, a menu will pop up. If we select `Help`, we will be presented with help around abusing this ACE, including:

<figure><img src="../../../../.gitbook/assets/image 6 (6).png" alt=""><figcaption></figcaption></figure>

Finally, we can use the pre-built queries in BloodHound to confirm that the `adunn` user has DCSync rights.

**Viewing Pre-Build queries through BloodHound**

<figure><img src="../../../../.gitbook/assets/image 7 (4).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
any user at any group was nested with other groups it will inherit the privileges of this group
{% endhint %}
