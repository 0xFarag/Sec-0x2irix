# MSSQL

MSSQL is Microsoft’s relational database management system (RDBMS) that uses SQL. Unlike MySQL, MSSQL is **closed source** and was originally designed to run on **Windows**. It is widely used by database administrators and developers working with **Microsoft’s .NET framework** due to its strong integration with it.

Although there are versions of MSSQL available for **Linux and MacOS**, it is more commonly found on **Windows-based systems**.

is running on **port 1433**

***

#### **What is SSMS?**

**SQL Server Management Studio (SSMS)** is a tool used to manage **MSSQL** databases. It can be installed along with MSSQL or downloaded separately. Database admins and developers use it for **configuring and managing databases**.

A key point to note is that **SSMS is a client-side application**, meaning it can be installed on any system that needs to connect to a database, not just on the database server itself. This can be a security risk—if an attacker finds a **vulnerable system with SSMS and stored credentials**, they could use it to access the database and perform malicious actions.

***

#### Footprinting

We can gather **useful information** about an MSSQL server by running specific **Nmap scans**. MSSQL typically listens on **TCP port 1433**, and Nmap has built-in scripts to query the service.

🔹 **Example Nmap scan:**

1st way

```bash
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

```

**Key Findings:**

* **Hostname & MSSQL Version**
* **Named Pipes Enabled**
* **Open Ports & Authentication Info**
*   **Potential Weak Passwords**

    ***

2nd way

Metasploit has an **auxiliary module** called **mssql\_ping** that can retrieve server details.

🔹 **Example Metasploit scan:**

```bash
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248
msf6 auxiliary(scanner/mssql/mssql_ping) > run
```

**Key Findings:**

* **Server Name & Instance Name**
* **SQL Version**
*   **Connection Methods (TCP - Named Pipes)**

    ***

If we obtain **valid credentials**, we can **interact with MSSQL directly** using [**mssqlclient.py**](http://mssqlclient.py) from Impacket.

🔹 **Example connection command:**

```bash
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```

**Post-Connection Actions:**

* Execute **T-SQL Queries**
*   List available databases:

    ```sql
    SQL> select name from sys.databases;
    ```
* Attempt **privilege escalation** or **data extraction**

***

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-19 220029.png" alt=""><figcaption></figcaption></figure>

**List All Databases**

```bash
SELECT name FROM sys.databases;
```

#### **Identify Non-Default Databases**

MSSQL comes with the following default databases:

* **`master`**: Stores system configuration and metadata.
* **`tempdb`**: Used for temporary tables and operations.
* **`model`**: Template for new databases.
* **`msdb`**: Used by SQL Server Agent for scheduling and alerts.
