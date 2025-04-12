# SSH

**SSH (Secure Shell)** to establish a secure connection over an untrusted network. SSH runs on **port 22** and encrypts the communication to prevent data interception.

#### **SSH-1 vs. SSH-2**

* **SSH-1**: Old and vulnerable to attacks like MITM.
* **SSH-2**: More secure and recommended for use.

#### **Authentication Methods in SSH**

SSH offers six authentication methods, with the most important being:

1. **Password authentication**: Requires entering a password each time.
2. **Public-key authentication**: A more secure method using a key pair.

#### **How Public Key Authentication Works**

* The **private key** stays on your local machine and is secured with a passphrase.
* The **public key** is stored on the server.
* When connecting, the server sends a challenge encrypted with the public key.
* The client decrypts it using the private key and sends back the correct response.
* If successful, access is granted without needing a password.

***

#### Footprinting

To fingerprint an SSH server, we can use **ssh-audit**, a tool that inspects the SSH configuration, client, and server settings. This helps identify weak encryption algorithms that might be exploited.

**Using ssh-audit**

Run the following commands to clone and execute the tool:

```bash
git clone <https://github.com/jtesta/ssh-audit.git> && cd ssh-audit
./ssh-audit.py <IP>
```

The output will show:

* OpenSSH version
* Key exchange algorithms
* Host-key algorithms
* Any weak or deprecated settings

Older versions might be vulnerable to attacks like **CVE-2020-14145**, which allows a Man-in-the-Middle (MITM) attack.

**Checking Authentication Methods**

To see which authentication methods are available on the server:

```bash
ssh -v <username>@<IP>
```

Example output:

```bash
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```

This means the server supports:

1. **Public Key Authentication**
2. **Password Authentication**
3. **Keyboard-Interactive Authentication**

To force a specific authentication method, use:

```bash
ssh -v <username>@<IP> -o PreferredAuthentications=password
```

#### **Understanding SSH Banners**

Different SSH banners indicate which protocol versions are supported:

* **SSH-1.99-OpenSSH\_3.9p1** → Supports both SSH-1 and SSH-2.
* **SSH-2.0-OpenSSH\_8.2p1** → Supports only SSH-2.
