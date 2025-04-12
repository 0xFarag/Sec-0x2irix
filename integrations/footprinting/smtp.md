# SMTP

**Simple Mail Transfer Protocol (SMTP)** is the standard protocol used for **sending emails over the internet**. It operates between **email clients (such as Outlook or Gmail) and mail servers, or between different mail servers** to route messages across domains.

#### **How SMTP Works?**

1. **Establishing Connection:**
   * The email client connects to the **SMTP server** via a specific **port (25, 587, or 465)**.
2. **Authentication:**
   * Some SMTP servers require **username and password authentication** to ensure the sender is authorized

#### **What is ESMTP?**

🔹 **Extended SMTP (ESMTP)** is an enhanced version of SMTP, commonly `used today.`

🔹 It supports **TLS encryption** via **STARTTLS**, which upgrades the connection to a secure SSL/TLS channel.

***

To interact with an SMTP server, use the telnet tool to establish a TCP connection on port 25 (e.g., `telnet 10.129.14.128 25`). Once connected, initiate the session with the **HELO** or **EHLO** command (e.g., `EHLO [example.com](<http://example.com/>)`) to identify yourself to the server. This allows you to manually test the SMTP server, send emails, or check for vulnerabilities. The server will respond with a confirmation and available features if the session is successfully started.

The command `VRFY` can be used to enumerate existing users on the system

```bash
htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root
```

***

#### **Footprinting**

```bash
nmap 10.129.14.128 -sC -sV -p25
```

**Nmap - Open Relay**

```bash
nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```

open relay which use to send email to any user

```bash
telnet 10.129.14.128 25
EHLO example.com
MAIL FROM:<fake@nowhere.com>
RCPT TO:<real@somewhere.com>
DATA
Subject: Test Email
This is a test.
.
QUIT
```

***

**you can discover version of SMTP**

**1st way**

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-16 200234.png" alt=""><figcaption></figcaption></figure>

**2nd way**

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-16 200359.png" alt=""><figcaption></figcaption></figure>

**3rd way**

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-16 200438.png" alt=""><figcaption></figcaption></figure>

now you can brute force on username

```bash
use auxiliary/scanner/smtp/smtp_enum
set RHOSTS <Target-IP>
run
```

```bash
smtp-user-enum -M VRFY -U users.txt -t <Target-IP>
```

**wordlist**: [https://github.com/jeanphorn/wordlist/blob/master/usernames.txt](https://github.com/jeanphorn/wordlist/blob/master/usernames.txt)

***

#### **SMTP Status Codes Table**

| **Code** | **Meaning**                    | **Description**                                     |
| -------- | ------------------------------ | --------------------------------------------------- |
| **2xx**  | **Success**                    | Request was successfully processed.                 |
| **220**  | Service Ready                  | SMTP server is ready to accept requests.            |
| **221**  | Closing Connection             | Server is closing the session.                      |
| **250**  | Requested Action Completed     | Command was successfully executed.                  |
| **3xx**  | **Intermediate Reply**         | More actions are needed before completion.          |
| **354**  | Start Mail Input               | Server is ready to receive email data.              |
| **4xx**  | **Temporary Failure**          | The request failed but can be retried.              |
| **421**  | Service Not Available          | Server is shutting down or temporarily unavailable. |
| **450**  | Mailbox Unavailable            | Recipient’s mailbox is temporarily inaccessible.    |
| **451**  | Server Error                   | Action aborted due to a local error.                |
| **5xx**  | **Permanent Failure**          | The request failed and should not be retried.       |
| **500**  | Syntax Error                   | Invalid command or parameters.                      |
| **501**  | Invalid Arguments              | Command not recognized due to invalid syntax.       |
| **530**  | Authentication Required        | SMTP authentication is required.                    |
| **550**  | Mailbox Unavailable (Rejected) | Email rejected due to policy or user not found.     |
| **552**  | Exceeded Storage Limit         | Recipient’s mailbox has exceeded its quota.         |
| **554**  | Transaction Failed             | Message rejected (common in spam filtering).        |
|          |                                |                                                     |
