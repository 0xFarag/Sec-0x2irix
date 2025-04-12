---
description: >-
  notion :
  https://wise-monitor-956.notion.site/Check-list-1bc1d1e7e13c80f99560ff0f3aae6c8b
---

# Check List

* **FTP - 21**
  * \[ ] scan service `nmpa -sV -sC -A -p 21 IP_add`
  * \[ ] try anonymous login `ftp ip_add`
  * \[ ] to display information about TLS/SSL `openssl s_client -connect 10.129.14.136:21 -starttls ftp`
  *   \[ ] if we success to login try to download file

      `wget -m --no-passive <ftp://anonymous:anonymous@10.129.14.136`>
  * \[ ]
* **SMB - 137:139,445**
  * \[ ] scan service `nmpa -sV -sC -p 445,137,138,139 192.168.1.5`
  * \[ ] explore shared file `smbclient -N -L [//10.129.14.128](<https://10.129.14.128/>)`
  * \[ ] access shared file `smbclient [//10.129.14.128/notes](<https://10.129.14.128/notes>)`# added name of file
  * \[ ] retrieve information like user account, shared file, etc `rpcclient -U "" ip_add`
  * \[ ] retrieve shared file which you can use `smbmap -H 10.129.14.128`
  *   \[ ] try to connect on smb by any credential

      `crackmapexec smb 10.129.14.128 --shares -u '' -p '’`
  * \[ ] to automate this process use `./enum4linux-ng.py 10.129.14.128 -A`
* **NFS - 2049**
  * \[ ] scan service `nmap -sV -A -p 111,2049 10.129.14.128`
  * \[ ] show shared file or privileges `nmap --script nfs* 10.129.14.128 -sV -p 111,2049`
  * \[ ] show shared file-mounting `showmount -e 10.129.14.128`
  * \[ ] mount with machine `sudo mount -t nfs 10.129.14.128:/mnt/nfs ./target-NFS/ -o nolock`
* **SMTP - 25,465,587**
  * \[ ] scan service `nmap 10.129.14.128 -sC -sV -p 25,465,587`
  * \[ ] try to connect on SMTP `telnet ip_add 25`
  * \[ ] check open relay `nmap 10.129.14.128 -p25 --script smtp-open-relay -v`
  * \[ ] after using telnet try to check users `VRFY user_name`
  * \[ ] brute force on username `auxiliary/scanner/smtp/smtp_enum` #msfconsole
  * \[ ] another way to brute force `smtp-user-enum -M VRFY -U users.txt -t <Target-IP>`
  * \[ ] wordlist link : ‣
* **DNS - 53**
  * \[ ] to get the DNS server `dig +noall +answer [google.com](<http://google.com/>) NS`
  * \[ ] to get ipv4 address `dig +noall +answer [microsoft.com](<http://microsoft.com/>) A`
  * \[ ] mail server `dig +noall +answer [microsoft.com](<http://microsoft.com/>) MX`
  * \[ ] alias name `dig +noall +answer [microsoft.com](<http://microsoft.com/>) CNAME`
  * \[ ] to get ipv6 `dig +noall +answer [microsoft.com](<http://microsoft.com/>) AAAA`
  * \[ ] to get all service `dig +noall +answer [microsoft.com](<http://microsoft.com/>) ANY`
  * \[ ] to search in specific dns server `dig +noall +answer @spacific_DNS_Server [microsoft.com](<http://microsoft.com/>) ANY`
  * \[ ] (SOA) record includes administrative information about your zone `dig +noall +answer [microsoft.com](<http://microsoft.com/>) soa`
  * \[ ] DNS server version `dig CH TXT version.bind @spacific_DNS_Server`
  * \[ ] zone transfer `dig @mircosoft.com [8x8.com](<http://8x8.com/>) axfr`
  * \[ ] try to use `nslookup`
* **IMAP/POP3 - 143,110,995,993**
  * \[ ] Scan network `nmap 10.129.14.128 -sV -p110,143,993,995 -sC`
  * \[ ] connect with `openssl s_client -connect 10.129.14.128:993 -quiet`
  * \[ ] test login `curl -k 'imaps://10.129.14.128' --user username:password -v`
  * \[ ] manual connect with netcat `nc --ssl 10.129.14.128 993`
  * \[ ] fetch email on server `openssl s_client -connect 10.129.14.128:imaps`
* **SNMP - 161,162**
  * \[ ] retrieves OIDs `snmpwalk -v2c -c public 10.129.14.128`
  * \[ ] brute-forcing **Community Strings** `onesixtyone -c seclists/Discovery/SNMP/snmp.txt 10.129.14.128`
  * \[ ] brute-force OIDs `braa [public@10.129.14.128](<mailto:public@10.129.14.128>):.1.3.6.*`
* **MySQL - 3306**
  * \[ ] scan service `sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*`
  * \[ ] connect without password `mysql -u root -h 10.129.14.128`
  * \[ ] connect with pass `mysql -u root -p0xpass -h 10.129.14.128`
* **MSSQL - 1433**
  * \[ ] Scan `sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248`
  * \[ ] \*\*\*\*Metasploit scan `msf6 auxiliary(scanner/mssql/mssql_ping)`
  * \[ ] connect to database `python3 [mssqlclient.py](<http://mssqlclient.py/>) [Administrator@10.129.201.248](<mailto:Administrator@10.129.201.248>) -windows-auth`
* **TNS - 1521**
  * \[ ] Scan `sudo nmap -p1521 -sV <target-IP> --open`
  * \[ ] brute force on SID `sudo nmap -p1521 -sV <target-IP> --open --script oracle-sid-brute`
  * \[ ] enumerate database component `./odat.py all -s <target-IP>`
  * \[ ] login by valid credential `sqlplus scott/tiger@<target-IP>/XE`
  * \[ ] exploit file upload `./odat.py utlfile -s <target-IP> -d XE -U scott -P tiger --sysdba --putFile C:\\\\\\\\inetpub\\\\\\\\wwwroot testing.txt ./testing.txt`
  * \[ ] verify file upload `curl -X GET http://<target-IP>/testing.txt`
* **IPMI - 623**
  * \[ ] Scan service `nmap -sU --script ipmi-version -p 623 <target-IP>`
  * \[ ] version scanner `use auxiliary/scanner/ipmi/ipmi_version`
  * \[ ] dump hash `use auxiliary/scanner/ipmi/ipmi_dumphashes`
  * \[ ] crack hash `hashcat -m 7300 ipmi_hash.txt rockyou.txt`
* **SSH - 22**
  * \[ ] version, host key algorithm `./ssh-audit.py <IP>`
  * \[ ] check auth method `ssh -v <username>@<IP>`
  * \[ ] force specific method `ssh -v <username>@<IP> -o PreferredAuthentications=password`
* **Rsync - 873**
  * \[ ] scan `sudo nmap -sV -p 873 127.0.0.1`
  * \[ ] listen shared directory `nc -nv 127.0.0.1 873`
  * \[ ] enumerate files `rsync -av --list-only rsync://127.0.0.1/dev`
  * \[ ] retrieve files `rsync -av rsync://127.0.0.1/dev .`
  * \[ ] make run it over ssh `rsync -av -e ssh rsync://127.0.0.1/dev .`
  * \[ ] using no standard port `rsync -av -e "ssh -p2222" rsync://127.0.0.1/dev .`
* **R-Services - 512:514**
  * \[ ] scan service `sudo nmap -sV -p 512,513,514 <IP>`
  * \[ ] .rhosts is misconfigured `rlogin <IP> -l <user>`
* **RDP - 3389**
  * \[ ] scan serv `nmap -sV -sC 10.129.201.248 -p3389 --script rdp*`
  * \[ ] deeper analysis `nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n`
  * \[ ] initiate session `xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248`
* **WinRm - 5985,5986**
  * \[ ] `nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n`
* **WMI - 135**
  * \[ ] `wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname”`
