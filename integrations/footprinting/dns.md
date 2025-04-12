# DNS

DNS (Domain Name System) is a decentralized system that translates domain names into IP addresses. It is essential for Internet functionality. run on port 53

**Main types of DNS servers:**

1️⃣ **Root Servers** – Store top-level domain (TLD) info.

2️⃣ **Authoritative Name Servers** – Provide original domain data.

3️⃣ **Non-Authoritative Name Servers** – Serve cached responses.

4️⃣ **Caching Servers** – Speed up lookups by storing queries.

5️⃣ **Forwarding Servers** – Forward requests to other DNS servers.

6️⃣ **Resolvers** – Process user queries and return results.

***

**privacy & security**, IT experts use **DNS encryption methods** like:

1️⃣ **DNS over TLS (DoT)** – Encrypts DNS queries via TLS.

2️⃣ **DNS over HTTPS (DoH)** – Uses HTTPS to secure requests.

3️⃣ **DNSCrypt** – Encrypts traffic between the device & name server.

DNS also stores additional **domain-related information**, such as:

**Mail server records (MX)**

**Name server records (NS)**

**DNS leaks** can expose critical information if not secured properly! 🚀

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-16 104651.png" alt=""><figcaption></figcaption></figure>

| `A`     | Returns an IPv4 address of the requested domain as a result.                                                                                                                                                                                                                                                                                |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AAAA`  | Returns an IPv6 address of the requested domain.                                                                                                                                                                                                                                                                                            |
| `MX`    | Returns the responsible mail servers as a result.                                                                                                                                                                                                                                                                                           |
| `NS`    | Returns the DNS servers (nameservers) of the domain.                                                                                                                                                                                                                                                                                        |
| `TXT`   | This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.                                                                                           |
| `CNAME` | This record serves as an alias for another domain name. If you want the domain [www.hackthebox.eu](http://www.hackthebox.eu) to point to the same IP as [hackthebox.eu](http://hackthebox.eu), you would create an A record for [hackthebox.eu](http://hackthebox.eu) and a CNAME record for [www.hackthebox.eu](http://www.hackthebox.eu). |
| `PTR`   | The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.                                                                                                                                                                                                                               |
| `SOA`   | Provides information about the corresponding DNS zone and email address of the administrative contact.                                                                                                                                                                                                                                      |

The `SOA` record is located in a domain's zone file and specifies who is responsible for the operation of the domain and how DNS information for the domain is managed.

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-16 105339.png" alt=""><figcaption></figcaption></figure>

which dot <mark style="color:red;">`.`</mark> in \[`postmaster8x8.com](<http://postmaster8x8.com>)` refer to <mark style="color:red;">`@`</mark>

***

**DNS Default Configuration: Key Files and Their Roles**

DNS servers rely on three main configuration file types to manage domain name resolution effectively:

1. **Local DNS Configuration Files** – Define the general settings of the DNS server.
2. **Zone Files** – Store domain-specific records, mapping domain names to IP addresses.
3. **Reverse Name Resolution Files** – Handle reverse lookups, converting IP addresses back to domain names.

**Key Configuration Files in BIND9 (Linux DNS Server)**

* **`named.conf.local`** – Specifies the DNS zones managed by the server.
* **`named.conf.options`** – Contains global settings, such as security policies and forwarders.
* **`named.conf.log`** – Records server activity, errors, and debugging information.

***

useful: [https://www.cvedetails.com/](https://www.cvedetails.com/)

#### **Footprinting the Service**

```bash
dig +noall +answer google.com NS # to get the DNS server
dig +noall +answer google.com TXT # Some records may include verification keys for third-party services (e.g., Google, Microsoft).
dig +noall +answer microsoft.com A # to get ipv4 address
dig +noall +answer microsoft.com MX # mail server
dig +noall +answer microsoft.com CNAME # alias names
dig +noall +answer microsoft.com AAAA # ipv6 
dig +noall +answer microsoft.com ANY # to get all of the above
dig +noall +answer @spacific_DNS_Server microsoft.com ANY # to search in specific dns server like 8.8.8 or 1.1.1.1
dig +noall +answer microsoft.com soa
dig CH TXT version.bind @spacific_DNS_Server # 10.129.120.85 DNS server's version using a class CHAOS query and type TXT
dig @mircosoft.com 8x8.com axfr # zone transfer
```

***

**Subdomain Brute Forcing**

```bash
for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\\|SOA' | sed -r '/^\\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

```bash
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```

