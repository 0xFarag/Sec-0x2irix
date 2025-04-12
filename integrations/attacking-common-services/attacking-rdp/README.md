# Attacking RDP

Let’s break down the **Attacking RDP** section you provided. **Remote Desktop Protocol (RDP)** is a Microsoft protocol that lets you remotely connect to a computer with a graphical interface over a network. It’s widely used by admins to manage systems centrally and by managed service providers (MSPs) to handle client networks. However, its popularity makes it a juicy target for attackers.

#### Step 1: Enumeration

* RDP runs on **TCP/3389** by default. We use **Nmap** to check if this port is open and an RDP service is active. If it’s open, we’ve got a potential entry point.

#### Common Attack: Password Guessing

* RDP requires a username and password. Misconfigurations might leave it password-less (rare but possible). The go-to attack is **password guessing**, but Windows often locks accounts after too many failed attempts (Account Lockout).
* To avoid this, we use **Password Spraying**: Try one password across multiple usernames, then switch passwords, staying under the lockout threshold.

#### Tools for Password Attacks:

* **Crowbar**: Takes a list of usernames and one password, tests them against RDP, and flags any successful logins.
* **Hydra**: Similar idea—tries a single password across many usernames to find valid credentials.

#### Once You Have Credentials:

* Use tools like **rdesktop** or **xfreerdp** to log into the RDP server with the valid username and password, giving you a graphical interface to explore.

#### Advanced Attack: Session Hijacking

* If you gain access to a machine with a local admin account and another user is logged in via RDP, you can **hijack their session**—impersonate them without their password.
* **How?**
  * Use **tscon.exe** (a Microsoft tool) to switch to their session, but you need **SYSTEM** privileges.
  * If you only have admin rights, create a service with **sc.exe** that runs a command as SYSTEM, then hijack the session.
* If the hijacked user is a Domain Admin, you’ve hit the jackpot—full network control.

#### Pass-the-Hash (PtH) Attack:

* If you have a user’s **NTLM hash** (e.g., from a credential dump) but can’t crack it to a plaintext password, you can still use it to access RDP.
* **How?**
  * The target needs **Restricted Admin Mode** enabled, which isn’t default. Enable it by tweaking the **Registry** (DisableRestrictedAdmin key).
  * Then, use **xfreerdp** with the hash to log in, gaining GUI access.
* Note: This doesn’t work on all systems (e.g., Server 2019 patched it), but it’s worth a shot when GUI access is needed.

***

#### Steps:

1. **Enumeration:**
   * **Port**: TCP/3389.
   * **Tool**: Nmap to confirm RDP is running.
2. **Password Attacks:**
   * **Method**: Password Spraying (one password, many usernames).
   * **Goal**: Find valid credentials without triggering lockout.
3. **Session Hijacking:**
   * **Requirement**: Local admin + SYSTEM privileges.
   * **Goal**: Impersonate an active RDP user (e.g., Domain Admin).
4. **Pass-the-Hash (PtH):**
   * **Requirement**: NTLM hash + Restricted Admin Mode enabled.
   * **Goal**: RDP access without plaintext password.

#### Tools & Commands:

| **Tool/Command**                                                            | **What It Does**                                                |
| --------------------------------------------------------------------------- | --------------------------------------------------------------- |
| nmap -Pn -p3389 \<IP>                                                       | Scans TCP/3389 to detect RDP service.                           |
| crowbar -b rdp -s \<IP> -U \<file> -c \<pass>                               | Password sprays RDP with a username list and single password.   |
| hydra -L \<file> -p \<pass> \<IP> rdp                                       | Same as Crowbar, tests one password across usernames.           |
| rdesktop -u \<user> -p \<pass> \<IP>                                        | Logs into RDP with valid credentials.                           |
| query user                                                                  | Lists active RDP sessions (ID, username, session name).         |
| sc.exe create \<name> binpath= "cmd.exe /k tscon \<ID> /dest:\<session>"    | Creates a service to run tscon as SYSTEM for session hijacking. |
| tscon \<ID> /dest:\<session>                                                | Switches to another RDP session (needs SYSTEM).                 |
| reg add HKLM\System\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin | Enables Restricted Admin Mode for PtH.                          |
| xfreerdp /v:\<IP> /u:\<user> /pth:\<hash>                                   | Logs into RDP using an NTLM hash.                               |

#### Quick Tips:

* **Start with Nmap**: Verify RDP is active.
* **Spray Smart**: Avoid lockouts with Password Spraying.
* **Hijack Opportunity**: Look for active sessions with query user.
* **PtH Check**: Test if Restricted Admin Mode is on; if not, enable it.

***

