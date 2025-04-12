# Attacking SMTP

**SMTP (Simple Mail Transfer Protocol)** is a communication protocol for electronic mail transmission. The vulnerabilities in SMTP protocols can allow malicious attackers to spoof the origin of the messages, perform phishing attacks, and even execute code remotely in the system.

### **Connect**

#### **Connect Using Telnet**

We can use Telnet to connect to the remote server. Here is a command using Telnet:

```
telnet example.com 25

```

***

### **Enumeration**

#### **Identifying a SMTP Server**

You can use `Nmap` to check if there's an Telnet server on a target host like this:

```bash
nmap -p25,465,587 -sV -Pn target.com
```

#### **Enumerate Users**

Nmap has a script for SMTP user enumeration

```bash
nmap -p25 --script smtp-enum-users.nse target.com
```

#### **DNS Mail Exchange (MX) Record Enumeration**

We can use the dig tool to find out the mail servers (MX servers) of a domain. This tool sends a DNS query and returns the list of MX servers.

```bash
dig +short mx example.com

```

#### **Information Disclosure with NTLM Auth**

Some SMTP servers with NTLM Authentication enabled can disclose sensitive information, like Windows Server version and internal IP, if Anonymous Logon is allowed.

```bash
nmap -p25 --script smtp-ntlm-info --script-args smtp-ntlm-info.fingerprint=on target.com
```

***

### **Attack Vectors**

#### **Open Relay Exploit**

SMTP Open Relay occurs when the SMTP server is configured to accept and transfer messages on the network that were neither for nor from local users.

Here is a simple example of how to test for open relay:

```bash
telnet target.com 25
MAIL FROM:<test@example.com>
RCPT TO:<test2@anotherexample.com>
DATA
Subject: Test open relay
Test message
.
QUIT

```

#### SMTPTester Tool

which use to test 3 bugs → open relay, user enumerate, spoofing

```bash
git clone <https://github.com/xFreed0m/SMTPTester.git>
pip install -r requirements.txt
pip install -r requirements.txt
```

### **Post-Exploitation**

#### **Common SMTP Commands**

| **Command** | **Description**                          | **Usage**                                                        |
| ----------- | ---------------------------------------- | ---------------------------------------------------------------- |
| HELO        | Identifies the client to the server.     | HELO [example.com](http://example.com)                           |
| EHLO        | Extended HELLO.                          | EHLO [example.com](http://example.com)                           |
| MAIL FROM:  | Specifies the sender's email address.    | MAIL FROM: <[sender@example.com](mailto:sender@example.com)>     |
| RCPT TO:    | Specifies the recipient's email address. | RCPT TO: <[recipient@example.com](mailto:recipient@example.com)> |
| DATA        | Indicates the start of the message body. | DATA                                                             |
| RSET        | Resets the session.                      | RSET                                                             |
| NOOP        | No operation; used for testing.          | NOOP                                                             |
| QUIT        | Terminates the session.                  | QUIT                                                             |
