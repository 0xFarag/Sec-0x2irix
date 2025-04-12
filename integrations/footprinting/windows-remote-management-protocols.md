# Windows Remote Management Protocols

From Windows Server 2016 onwards, **Remote Management** is enabled by default, allowing administrators to manage servers remotely. The key methods include:

1. **RDP (Remote Desktop Protocol):** Enables GUI-based remote access via TCP **3389**. It supports **TLS/SSL** encryption but may accept self-signed certificates, making it vulnerable to attacks.
2. **WinRM (Windows Remote Management):** Uses **WS-Management** to allow remote administration via **PowerShell** and **WMI**.
3. **WMI (Windows Management Instrumentation):** Allows remote queries and management of system data via scripting.

💡 **Scanning RDP:**

You can use **Nmap** to check if RDP is enabled and extract system details:

```bash
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

For deeper analysis, use `--packet-trace` to inspect network packets.

```bash
 nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n
```

💡 **Cisco's RDP Security Check Tool:**

A Perl script called [**rdp-sec-check.pl**](http://rdp-sec-check.pl) helps identify RDP security settings:

```bash
git clone <https://github.com/CiscoCXSecurity/rdp-sec-check.git>
cd rdp-sec-check
./rdp-sec-check.pl 10.129.201.248
```

This tool determines if the server enforces **NLA (Network Level Authentication)** or allows weak encryption.

#### **Initiate an RDP Session**

```bash
 xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```

***

WinRM (**Windows Remote Management**) is a built-in protocol in Windows that allows **remote management** of other computers via the **command line**. It works by using **SOAP (Simple Object Access Protocol)** to establish a connection with remote hosts.

📌 **Ports Used by WinRM:**

* **5985 (HTTP)** → Unencrypted communication
* **5986 (HTTPS)** → Encrypted communication

Previously, **ports 80 and 443** were used, but due to security concerns, **5985 and 5986** are now the standard.

***

#### **🔹 What Can WinRM Do?**

* **Run commands remotely** on another machine.
* **Create a remote session** via **PowerShell** to manage the system.
* Works with **WinRS (Windows Remote Shell)** to execute commands on remote systems.
* **Enabled by default** in **Windows Server 2012 and later**, but must be configured manually in regular Windows versions.

***

#### **How to Check If WinRM is Enabled?**

**Using Nmap:**

To check if a remote machine has WinRM enabled, scan for its ports:

```bash
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

This will show whether the **WinRM service is running** on the target machine.

***

#### **How to Connect to a WinRM-Enabled Machine?**

**On Windows (PowerShell):**

```powershell
Test-WsMan -ComputerName 10.129.201.248
```

👨‍💻 **On Linux (Using Evil-WinRM):**

```bash
evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```

Once connected, you will get a **fully interactive shell** on the remote system.

***

**WMI (Windows Management Instrumentation)** is Microsoft's implementation of **CIM (Common Information Model)** for Windows systems. It provides **read and write access to nearly all system settings**, making it a powerful tool for **administration and remote management** of both PCs and servers.

📌 **Ways to Access WMI:**

* **PowerShell** ✅
* **VBScript** ✅
* **WMIC (Windows Management Instrumentation Console)** ✅

It is not a single program but a **collection of services and databases (repositories)** that help manage Windows environments.

***

#### **🔹 How WMI Works?**

* **Initiates communication on TCP port 135**
* **Then switches to a random port after connection**

***

#### **🔹 How to Test WMI?**

🔍 **Using Impacket's** [**wmiexec.py**](http://wmiexec.py)**:**

```bash
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```

✅ This command:

* Establishes an **SMB-based** connection
* Runs the **hostname** command remotely
* Returns the target machine's name
