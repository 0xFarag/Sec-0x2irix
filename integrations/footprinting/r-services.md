# R-Services

**R-Services** were the old way of remote access between Unix machines before **SSH** replaced them due to security flaws. They operate on **ports 512, 513, and 514** and use **r-commands** like **rlogin, rsh, and rexec** to allow file transfer and command execution between systems.

#### **Main Security Issue?**

These services transmit data **unencrypted**, making them vulnerable to **MITM attacks**, where an attacker can intercept credentials and commands.

#### **Key r-commands:**

* **rcp** → Copies files without warnings.
* **rsh** → Opens a shell on a remote system without authentication if trusted.
* **rexec** → Executes remote commands but requires credentials in plain text.
* **rlogin** → Remote login similar to **telnet**, only for Unix-like systems.

#### **How do they work?**

They rely on two files:

1. **/etc/hosts.equiv** (Global trusted hosts)
2. **.rhosts** (User-specific trusted hosts)

If these files are misconfigured, attackers can log in **without a password** and gain access to the system.

#### **How to detect them?**

Using **Nmap**, we can scan for open **R-services** ports:

```bash
sudo nmap -sV -p 512,513,514 <IP>
```

If they are open, it’s possible that **R-services** are in use.

#### **Exploitation Example:**

If **.rhosts** is misconfigured, we can use:

```bash
rlogin <IP> -l <user>
```

And log in without a password.

#### **Finding active users?**

* **rwho** → Lists currently logged-in users.
* **rusers** → Provides more details on remote users.

