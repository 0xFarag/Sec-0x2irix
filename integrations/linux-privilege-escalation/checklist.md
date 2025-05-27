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
