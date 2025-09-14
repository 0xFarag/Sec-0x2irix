# Users & Groups

**Create User**

```bash
net user nameOfuser * /add

or

net user nameOfuser password /add
```

**Delete User**

```bash
net user nameOfuser /delete
```

**Disable/Enable Account**

```bash
net user nameOfuser /active:no
or
net user nameOfuser /active:yes
```

**Display Password Policy**

```bash
net accounts
```

<figure><img src="../../../.gitbook/assets/image 3 (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

### **Users in Groups**

* Administrator : Full control
* Network operations: allow to modify network settings (IPs, DNS)
* Users

**Display Groups**

```bash
net localgroup
```

list group member

```bash
net localgroup administrators
```

<figure><img src="../../../.gitbook/assets/image 4 (3).png" alt=""><figcaption></figcaption></figure>

***

**Create Group**

```bash
net localgroup groupName /add
```

**Delete Group**

```bash
net localgreoup groupName /del
```

**Add user to group**

```bash
net localgroup groupName UserName /add
```

**Delete user to group**

```bash
net localgroup groupName UserName /del
```
