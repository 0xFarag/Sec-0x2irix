# Anonymous Machine

start to scan to discover ports and services is running

![Screenshot 2025-04-10 143942.png](<../../.gitbook/assets/Screenshot_2025 04 10_143942.png>)

then we wanna to know more info for this services

```bash
nmap -sV -sC -A -O Ip_add
```

![Screenshot 2025-04-10 143959.png](<../../.gitbook/assets/Screenshot_2025 04 10_143959.png>)

we found port 21 has `Anonymous login` so let’s try to discover it

![Screenshot 2025-04-10 144057.png](<../../.gitbook/assets/Screenshot_2025 04 10_144057.png>)

we found 3 files in folder call scripts after we download these files we discover her content for each file

![Screenshot 2025-04-10 144138.png](<../../.gitbook/assets/Screenshot_2025 04 10_144138.png>)

> this file used to automated a scheduled task

![Screenshot 2025-04-10 144151.png](<../../.gitbook/assets/Screenshot_2025 04 10_144151.png>)

so we edit the content of `clean.sh` to code take a reverse shell on this machine

![Screenshot 2025-04-10 144214.png](<../../.gitbook/assets/Screenshot_2025 04 10_144214.png>)

> then we listen this port on attacker machine and we upload this file by ftp server

![Screenshot 2025-04-10 144230.png](<../../.gitbook/assets/Screenshot_2025 04 10_144230.png>)

then let’s fix this shell to make it more flexible

![Screenshot 2025-04-10 144307.png](<../../.gitbook/assets/Screenshot_2025 04 10_144307.png>)

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
**Ctrl+Z**
stty raw -echo;fg
```

we start to discover files on this machine but nothing

![Screenshot 2025-04-10 144317.png](<../../.gitbook/assets/Screenshot_2025 04 10_144317.png>)

we try to search for `SUID files`

![Screenshot 2025-04-10 144334.png](<../../.gitbook/assets/Screenshot_2025 04 10_144334.png>)

we use `env` to take a root shell on this machine

![Screenshot 2025-04-10 144349.png](<../../.gitbook/assets/Screenshot_2025 04 10_144349.png>)

![Screenshot 2025-04-10 144407.png](<../../.gitbook/assets/Screenshot_2025 04 10_144407.png>)
