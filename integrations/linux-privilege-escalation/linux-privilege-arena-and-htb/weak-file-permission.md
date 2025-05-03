# Weak file permission

first start with to know the permissions of critical file like `/etc/shadow` `/etc/passwsd`

![image.png](<../../../.gitbook/assets/image (15).png>)

we found any user on the system can read the **shadow** file

try to unshadow passwords of this file

* get two file on attacker machine `/etc/shadow` `/etc/passwsd`
* then choose **unshadow** tool → which use to print user and hashed file

<figure><img src="../../../.gitbook/assets/image 1 (6).png" alt=""><figcaption></figcaption></figure>

* run john to crack this hash

<figure><img src="../../../.gitbook/assets/image 2 (5).png" alt=""><figcaption></figcaption></figure>

> shadow file must have privilege for other users is 0 privilege

> \--pot=deleteme.pot
