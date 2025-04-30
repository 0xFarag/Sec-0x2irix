# Base Enumeration

#### <mark style="color:purple;">Users</mark>

**Enumerate the current `user ID (UID`), `group ID (GID)`, and the groups the user belongs to.**

```bash
id
uid=1001(john) gid=1001(john) groups=1001(john),27(sudo) # john's result
```

**Enumerate basic information of all users using `/etc/passwd`.**

```bash
cat /etc/passwd
```

**Enumerate shadow file if is accessible**

```bash
cat /etc/shadow
unshadow /etc/passwd /etc/shadow
#then try choose jhon
```

* **Username**: The login name (1-32 characters).
* **Password**: An `x` means the password is stored in `/etc/shadow`.
* **User ID (UID)**: Unique ID for the user. **UID 0 is for root**, 1-99 are reserved, and 100-999 are for system accounts.
* **Group ID (GID)**: The primary group ID, found in `/etc/group`.
* **User Info (GECOS)**: Optional user information like full name or contact info.
* **Home Directory**: The user's default directory when logging in.
* **Shell**: The user's default shell, like `/bin/bash`, or `/sbin/nologin` to prevent login.

***

#### <mark style="color:purple;">System</mark>

**Enumerate hostname.**

```bash
hostname
```

**Enumerate operating system version**

```bash
cat /etc/os-release
```

```bash
cat /etc/issue
```

**Enumerate kernel version and architecture.**

```bash
cat /proc/version
# give information about kernal and compilers (gcc,..etc)
```

```bash
uname -a
```

***

#### <mark style="color:purple;">**User configurations**</mark>

**list `sudoer` capabilities of current user.**

> when your write this command the device check file `sudoer` if u have any permission of root or if u can login with root without password

```bash
sudo -l

User vourf may run the following commands on DESKTOP-JE4JGR0:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/lib/win-kex/xrdp/xrdpservice
    (root) NOPASSWD: /usr/lib/win-kex/wslg-sock/wslg-sock
```

**List environment variables.**

> which we can find tokens, passwords , maybe can u must know `PATH` inject a malicious file

```bash
env
```

***

#### <mark style="color:purple;">**Processes**</mark>

**Enumerate all processes in a user readable format.**

```bash
ps aux
```

Monitor Processes.

```bash
watch -n 1 "ps -aux | grep pass"
```

It also possible to monitor running processes at live time using [pspy](https://github.com/DominicBreuker/pspy) tool.

***

#### <mark style="color:purple;">Network</mark>

Enumerate all network interfaces, this includes physical and virtual networks.

```bash
ip a
```

```bash
ifconfig
```

Display the routing tables.

```bash
route
```

Enumerate connections.

```bash
ss -anp
```

```bash
netstat -tulnp
```

Enumerate firewall rules.

```
cat /etc/iptables/rules.v4
```

***

#### <mark style="color:purple;">Installed Software</mark>

```bash
dpkg -l
```

***

#### <mark style="color:purple;">SSH Keys & History</mark>

Check `~/.ssh/`, `.bash_history`, configs (`.gitconfig`, `config.json`, etc.)

***

#### <mark style="color:purple;">SUID/SGID Binaries:</mark>

```bash
find / -perm -4000 2>/dev/null
```

***

#### <mark style="color:purple;">Writable Directories</mark>

```bash
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```
