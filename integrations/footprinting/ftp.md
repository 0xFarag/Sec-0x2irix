# FTP

FTP connection has **two channels**

1. **Control Channel (Port 21):** For sending commands.
2. **Data Channel (Port 20):** For transferring files

### <mark style="color:red;">FTP vs TFTP</mark>&#x20;

* **FTP (File Transfer Protocol)**: Uses TCP (reliable), supports authentication, encryption, and advanced features like creating/deleting files. Best for secure, complex file transfers.
* **TFTP (Trivial File Transfer Protocol)**: Uses UDP (faster, less reliable), no authentication or encryption, only transfers files. Best for simple, quick tasks like booting devices.

**Key Difference**: FTP is secure and feature-rich; TFTP is fast and basic.

***

**download all file from ftp server**

```bash
wget -m --no-passive <ftp://anonymous:anonymous@10.129.14.136>
```

With the `PUT` command, we can upload files in the current folder to the FTP server.

```bash
ftp> put testupload.txt
```

| **Setting**                                          | **Description**                                                                                          |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `listen=NO`                                          | Run from `inetd` or as a standalone daemon?                                                              |
| `listen_ipv6=YES`                                    | Listen on IPv6 ?                                                                                         |
| `anonymous_enable=NO`                                | Enable Anonymous access?                                                                                 |
| `local_enable=YES`                                   | Allow local users to login?                                                                              |
| `dirmessage_enable=YES`                              | Display active directory messages when users go into certain directories?                                |
| `use_localtime=YES`                                  | Use local time?                                                                                          |
| `xferlog_enable=YES`                                 | Activate logging of uploads/downloads?                                                                   |
| `connect_from_port_20=YES`                           | Connect from port 20?                                                                                    |
| `secure_chroot_dir=/var/run/vsftpd/empty`            | Name of an empty directory                                                                               |
| `pam_service_name=vsftpd`                            | This string is the name of the PAM service vsftpd will use.                                              |
| `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem` | The last three options specify the location of the RSA certificate to use for SSL encrypted connections. |

<mark style="color:red;">**Restricting Users from Accessing FTP**</mark>

The file `/etc/ftpusers` is used to **block certain users** from logging into the FTP server.

To view the blocked users, run:

```bash
cat /etc/ftpusers
```

To block a user (e.g., `hacker`), add them to the file:

```bash
echo "hacker" >> /etc/ftpusers
```

**Manage service**: `systemctl restart vsftpd`

try aggressive scan

```bash
nmap -sV -A -p 21 10.0.2.4
```

<mark style="color:red;">**FTP**</mark>

| **Command**                                                 | **Description**                                                         |
| ----------------------------------------------------------- | ----------------------------------------------------------------------- |
| `ftp <FQDN/IP>`                                             | Interact with the FTP service on the target.                            |
| `nc -nv <FQDN/IP> 21`                                       | Interact with the FTP service on the target.                            |
| `telnet <FQDN/IP> 21`                                       | Interact with the FTP service on the target.                            |
| `openssl s_client -connect <FQDN/IP>:21 -starttls ftp`      | Interact with the FTP service on the target using encrypted connection. |
| `wget -m --no-passive <ftp://anonymous>:anonymous@<target>` | Download all available files on the target FTP server.                  |
