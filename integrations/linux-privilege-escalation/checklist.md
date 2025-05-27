# Checklist

* **users**
  * \[ ] `cat /etc/passwd`
  * \[ ] `cat /etc/shadow`
  * \[ ] `cat /etc/group`
  * \[ ] `cat /etc/fstab`
  * \[ ] scheduled task `cat /etc/crontab`
  * \[ ] display `history`
  * \[ ] display `/tmp , /var/tmp`
* **system**
  * \[ ] `hostname`
  * \[ ] enumerate OS version `cat /etc/os-release`
  * \[ ] kernel version `uname -a`
  * \[ ]
* **user configuration**
  * \[ ] shell escaping `sudo -l`
  * \[ ] environment variable `env`
  * \[ ]
* **process**
  * \[ ] process running `ps aux`
  * \[ ] monitor process`watch -n 1 "ps -aux | grep pass"`
* **Network**
  * \[ ] disable routing table `route`
  * \[ ] Lists devices the server communicates with.`arp -a`
  * \[ ] enumerate connection `ss -anp`
* **packed installed**
  * \[ ] `dpkg -l`
* **SUID/SGID**
  * \[ ] `find / -type f -perm -4000 -ls 2>/dev/null`
* **writable directories**
  * \[ ] `find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null`
* **system capability**
  * \[ ] `getcap -r / **2**>/dev/null`
* **hidden**
  * \[ ] `find / -type f -name ".*"` \*\*\*\*Finds hidden files
  * \[ ] `find / -type d -name ".*"` Finds hidden directories
* **version**
  * \[ ] search for any package version `ls /usr/bin | grep python`
* **Credential**
  *   \[ ] Find Config Files

      `find / -type f \\( -iname "*.conf" -o -iname "*.config" -o -iname "*.xml" -o -iname "*.ini" -o -iname "*.env" \\) 2>/dev/null`
  *   \[ ] Search for Credentials in Config Files

      `grep -iE 'user|username|pass|password|db_pass|db_user|auth|token' /etc/* 2>/dev/null`
  *   \[ ] Find Script Files

      `find / -type f \\( -iname "*.sh" -o -iname "*.py" -o -iname "*.pl" -o -iname "*.php" \\) 2>/dev/null`
  *   \[ ] Look for Backup Files

      `find / -type f \\( -iname "*.bak" -o -iname "*.backup" -o -iname "*.old" -o -iname "*.save" \\) 2>/dev/null`
  *   \[ ] Inspect Shell History

      ```bash
      cat ~/.bash_history
      cat ~/.zsh_history
      cat ~/.sh_history
      ```
  *   \[ ] Search Web Root for Credentials

      ```bash
      grep -iE 'user|username|pass|password' /var/www/html/* 2>/dev/null
      ## Example: WordPress
      cat /var/www/html/wp-config.php | grep 'DB_USER\\|DB_PASSWORD'
      ```
  *   \[ ] Check Spool & Mail Directories

      ```bash
      ls /var/mail/
      cat /var/mail/<username>  # Replace <username> accordingly

      ls /var/spool/mail/
      cat /var/spool/mail/<username>
      ```
  *   \[ ] Check for SSH Keys

      ```bash
      ls ~/.ssh
      cat ~/.ssh/id_rsa        # Private key
      cat ~/.ssh/known_hosts   # Previous connections
      ```
  *   \[ ] Global Credential Grep (Brute-force style)

      ```bash
      grep -rEi 'user|username|pass|password|token|secret|auth' / 2>/dev/null
      ```
  *   \[ ] Inspect /etc/fstab for Credentials

      ```bash
      cat /etc/fstab | grep -iE 'user|username|pass|password'
      ```

***

**Key Commands and Their Purpose**:

1. **whoami**: Displays the current user.
2. **id**: Shows user ID, group IDs, and group memberships.
3. **hostname**: Reveals the server’s name, which may indicate its role.
4. **ifconfig / ip a**: Lists network interfaces and subnets for potential lateral movement.
5. **sudo -l**: Checks if the user can run commands as root without a password.
6. **cat /etc/os-release**: Identifies the OS distribution and version.
7. **uname -a**: Shows the Kernel version to check for known exploits.
8. **echo $PATH**: Displays the PATH variable, which could be misconfigured for privilege escalation.
9. **env**: Lists environment variables, potentially revealing sensitive data like passwords.
10. **cat /etc/passwd**: Lists all users, their UIDs, GIDs, and shells.
11. **cat /etc/group**: Shows groups and their members to identify high-privilege groups.
12. **cat /etc/shells**: Lists available shells, which may have vulnerabilities.
13. **lsblk**: Enumerates block devices (e.g., unmounted drives that may contain sensitive data).
14. **df -h**: Displays mounted filesystems and their usage.
15. **cat /etc/fstab**: Checks for credentials or misconfigured mounts.
16. **route / netstat -rn**: Shows the routing table for network connections.
17. **arp -a**: Lists devices the server communicates with.
18. **find / -type f -name ".\*"**: Finds hidden files that may contain sensitive data.
19. **find / -type d -name ".\*"**: Finds hidden directories.
20. **ls -l /tmp /var/tmp /dev/shm**: Checks temporary directories for exploitable files.

**Key Areas to Investigate**:

* **OS and Kernel**: Look for outdated versions with known exploits (e.g., check Exploit-DB).
* **Running Services**: Identify services running as root (e.g., Nagios, Samba) for misconfigurations or vulnerabilities.
* **Users and Groups**: Check for users in high-privilege groups like sudo or adm.
* **File Permissions**: Look for writable files/directories owned by root or misconfigured PATH.
* **Sensitive Files**: Search for config files (.conf, .config), SSH keys, or .bash\_history containing credentials.
* **Defenses**: Identify protections like AppArmor, SELinux, or iptables that may block exploits.
* **Network**: Map subnets and connected devices for lateral movement.
