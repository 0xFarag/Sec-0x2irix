# Sudo

**`/etc/sudoers`**: Defines which users or groups can run specific commands and with what privileges.

**Default Settings**: Includes `env_reset` to clear the environment and `secure_path` to define the system paths for secure execution.

* **CVE-2021-3156 (Heap Overflow)**: A vulnerability in certain versions of `sudo` that allowed privilege escalation through a heap-based buffer overflow. To check the version, use `sudo -V`, then exploit the vulnerability using a PoC to get root access.
* **CVE-2019-14287 (Policy Bypass)**: A vulnerability in versions prior to `1.8.28` that allowed privilege escalation by specifying a user ID (UID) in a specific format (e.g., `sudo -u#-1`), which resulted in running the command as root.
