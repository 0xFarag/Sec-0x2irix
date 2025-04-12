# Interacting with Common Services

### **1. File Sharing Services**

File sharing services allow users to share and access files over a network. Common protocols include:

* **SMB (Server Message Block):** Used mainly in Windows for file and printer sharing.
* **FTP (File Transfer Protocol):** Standard protocol for transferring files between clients and servers.
* **SFTP (Secure File Transfer Protocol):** Secure version of FTP using SSH.
* **TFTP (Trivial File Transfer Protocol):** Simple, lightweight version of FTP.
* **NFS (Network File System):** Used in Linux environments to share files across networks.

***

#### **1.1 Interacting with SMB (Windows File Sharing)**

SMB is widely used in **Windows environments**. You can interact with it using GUI, command-line tools, or specialized penetration testing tools.

#### **Accessing an SMB Share in Windows**

*   Open "Run" (`Win + R`), type:

    ```
    \\\\<IP>\\<SharedFolder>
    ```

    Example:

    ```
    \\\\192.168.1.100\\Public
    ```
*   Using Command Prompt (CMD) to list files in an SMB share:

    ```
    dir \\\\<IP>\\<SharedFolder>
    ```

    Example:

    ```
    dir \\\\192.168.1.100\\Public
    ```
*   Mapping a network drive to an SMB share:

    ```
    net use Z: \\\\<IP>\\<SharedFolder> /user:<Username> <Password>
    ```

    Example:

    ```
    net use Z: \\\\192.168.1.100\\Public /user:admin P@ssw0rd
    ```
*   Disconnecting a mapped network drive:

    ```
    net use Z: /delete
    ```

#### **Interacting with SMB in Linux**

*   Enumerate available SMB shares using `smbclient`:

    ```bash
    smbclient -L //<IP> -U <Username>

    ```

    Example:

    ```bash
    smbclient -L //192.168.1.100 -U admin
    ```
*   Connect to an SMB share:

    ```bash
    smbclient //<IP>/<SharedFolder> -U <Username>

    ```

    Example:

    ```bash
    smbclient //192.168.1.100/Public -U admin
    ```
*   Mount an SMB share:

    ```bash
    sudo mount -t cifs //<IP>/<SharedFolder> /mnt/<MountPoint> -o username=<Username>,password=<Password>

    ```

    Example:

    ```bash
    sudo mount -t cifs //192.168.1.100/Public /mnt/smb -o username=admin,password=P@ssw0rd
    ```

***

#### **1.2 Interacting with FTP (File Transfer Protocol)**

#### **Using Windows CMD**

*   Connect to an FTP server:

    ```
    ftp <IP>
    ```

    Example:

    ```
    ftp 192.168.1.100
    ```

#### **Using Linux Terminal**

*   Connect to an FTP server:

    ```bash
    ftp <IP>
    ```
*   Use `wget` to download a file:

    ```bash
    wget ftp://<Username>:<Password>@<IP>/<file>
    ```

    Example:

    ```bash
    wget <ftp://admin:P@ssw0rd>@192.168.1.100/secret.txt
    ```
*   Use `curl` to upload a file:

    ```bash
    curl -T <file> ftp://<Username>:<Password>@<IP>/
    ```

    Example:

    ```bash
    curl -T myfile.txt <ftp://admin:P@ssw0rd>@192.168.1.100/
    ```

***

### **2. Email Services**

Email communication relies on different protocols:

* **SMTP (Simple Mail Transfer Protocol):** Used for sending emails.
* **IMAP (Internet Message Access Protocol):** Used for retrieving emails while keeping them on the server.
* **POP3 (Post Office Protocol v3):** Used for downloading emails and removing them from the server.

#### **2.1 Using Telnet to Test SMTP**

*   Connect to an SMTP server:

    ```
    telnet <SMTP-Server> 25
    ```

    Example:

    ```
    telnet mail.example.com 25
    ```
*   Send an email manually:

    ```
    HELO example.com
    MAIL FROM: <user@example.com>
    RCPT TO: <recipient@example.com>
    DATA
    Subject: Test Email
    Hello, this is a test email.
    .
    QUIT
    ```

***

### **3. Databases (SQL Services)**

Databases store and manage data for applications. The most commonly used database servers are:

* **MySQL/MariaDB:** Open-source database system used in web applications.
* **MSSQL (Microsoft SQL Server):** Enterprise-level database used in corporate environments.

***

#### **3.1 Connecting to MySQL**

#### **Using Linux**

*   Connect to a MySQL database:

    ```bash
    mysql -u <Username> -p<Password> -h <IP>
    ```

    Example:

    ```bash
    mysql -u root -pMySecurePass -h 192.168.1.100
    ```

***

#### **3.2 Connecting to MSSQL (Microsoft SQL Server)**

#### **Using Windows (sqlcmd)**

*   Connect to an MSSQL database:

    ```
    sqlcmd -S <IP> -U <Username> -P <Password>
    ```

    Example:

    ```
    sqlcmd -S 192.168.1.100 -U sa -P MySecurePass
    ```
*   List available databases:

    ```sql
    SELECT name FROM sys.databases;
    ```
