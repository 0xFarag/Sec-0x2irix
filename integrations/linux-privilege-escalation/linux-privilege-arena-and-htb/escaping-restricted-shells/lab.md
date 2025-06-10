# Lab

try to login with usually way

```bash
 ssh htb-user@10.129.205.109
```

when u try usually command like `ls,cd,whoami` we notice error refer to shell is rbash

![image.png](<../../../../.gitbook/assets/image (24).png>)

The command “`compgen -c`” gave me a list of allowed commands that I could make use of.

<figure><img src="../../../../.gitbook/assets/image 1 (11).png" alt=""><figcaption></figcaption></figure>

I did perhaps tried to see if I can create functions and at my surprise I could , let’s see if I can abuse this in any way.

Because of the restriction on “/” I have encountered few difficulties , whatsoever I decided to adopt a different approach: Tried to override the login shell and launch bash , however initially I have still encountered rbash behaviour so I tried pressing Ctrl+C which killed the rbash process and dropped me into the actually bash shell beneath it.

```bash
 ssh -t htb-user@10.129.205.109 bash
```

<figure><img src="../../../../.gitbook/assets/image 2 (8).png" alt=""><figcaption></figcaption></figure>
