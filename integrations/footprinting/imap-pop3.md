# IMAP/POP3

#### **What is IMAP?**

IMAP (Internet Message Access Protocol) allows users to manage emails directly on a mail server without downloading them. Unlike POP3, IMAP supports access from multiple devices while keeping changes synchronized.

#### **Key Features:**

* Emails remain on the server until deleted.
* Allows access from multiple devices with real-time synchronization.
* Supports folder organization for better email management.
* Multiple users can access emails at the same time.

#### **How it Works:**

1. **Connects to the server** via **port 143**.
2. **Authenticates** using a username and password.
3. **Manages emails** (view, delete, move, and reply) without downloading them.
4. **Synchronizes** across all connected devices.

> we can use **pop3** in **offline mode** because have **async access** in this mode

#### **IMAP Encryption**

* By default, IMAP **transmits data in plaintext**, making it vulnerable.
* **SSL/TLS encryption** is used for secure communication:
  * **Port 143** for encrypted connections after session start.
  * **Port 993** for fully encrypted sessions.

> pop3 default ports 110,995 which 995 for full encrypted session

***

### Footprinting

to Scan netwok

```bash
nmap 10.129.14.128 -sV -p110,143,993,995 -sC
```

**IMAP/POP3 Connection**: Use to test login and get details (e.g., `TLS version, certificate`).

```bash
curl -k 'imaps://10.129.14.128' --user username:password -v
```

**Manual IMAP with OpenSSL**: Connect withand issue IMAP commands (e.g., `A001 LOGIN cry0l1t3 1234`).

```bash
openssl s_client -connect 10.129.14.128:993 -quiet
```

**Manual IMAP with Ncat**: Use to interact manually.

```bash
ncat --ssl 10.129.14.128 993
```

**Delay Emails (Testing)**: Add delay with a Python script: `time.sleep(5)` in `smtplib.SMTP("10.129.14.128", 25).sendmail(...).`

***

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-17 060213.png" alt=""><figcaption></figcaption></figure>

in this lab we success to read inbox mails

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-17 061232.png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-17 134953.png" alt=""><figcaption></figcaption></figure>

```bash
1 **LOGIN** robin robin
1 **LIST "" ***
**1** **SELECT** DEV.DEPARTMENT.INT
**1 FETCH 1 ALL**

## commands which use in openssl
```
