# ACL Abuse Primer

**Access control list:** which define the authorization for each user

**ACM (Access Control Mechanism/Model)**: the way these permissions are applied—inheritance rules, overriding, and how the system enforces .

**token** → which is define with each session contain some of information like SID, Priv, Group membership)

**Access Control Entries - ACEs→** The settings themselves in an ACL

> every object have ACL but can have multiple ACEs

### Types of ACLs

<mark style="color:red;">**DACL (Discretionary Access Control List):**</mark> defines which security principals are granted or denied access to an object

<mark style="color:red;">**SACL (System Access Control Lists):**</mark> allow administrators to log access attempts made to secured objects.

![image.png](<../../../../.gitbook/assets/image (47).png>)

### **SACL**

![](<../../../../.gitbook/assets/2025 06 26_20_10_24 SACL_example.png_(1393636)_ _Brave.png>)

### **Access Control Entries (ACEs)**

**contain 3 main types** `Access denied/allowed ACE for DACL` and `system audit ACE for SACL`

**Have 4 Component**

* The security identifier (SID) of the user/group to access object or SPN
* flag to define type of ACE (access denied, allowed, or system audit ACE)
* A set of flags define children can inherit privileges from parent or not
*   access mask which is a 32-bit value that defines the rights granted to an object

    ![](<../../../../.gitbook/assets/2025 06 26_20_24_13 ACE_example.png_(1394637)_ _Brave.png>)

***

**Some example Active Directory object security permissions are as follows. These can be enumerated (and visualized) using a tool such as BloodHound, and are all abusable with PowerView, among other tools:**

* `ForceChangePassword` abused with `Set-DomainUserPassword`
* `Add Members` abused with `Add-DomainGroupMember`
* `GenericAll` abused with `Set-DomainUserPassword` or `Add-DomainGroupMember`
* `GenericWrite` abused with `Set-DomainObject`
* `WriteOwner` abused with `Set-DomainObjectOwner`
* `WriteDACL` abused with `Add-DomainObjectACL`
* `AllExtendedRights` abused with `Set-DomainUserPassword` or `Add-DomainGroupMember`
* `Addself` abused with `Add-DomainGroupMember`

***

<figure><img src="../../../../.gitbook/assets/image 2 (16).png" alt=""><figcaption></figcaption></figure>
