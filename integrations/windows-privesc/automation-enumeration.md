# Automation Enumeration

### <mark style="color:red;">**WinPEAS**</mark>

WinPEAS is part of the PEAS (Privilege Escalation Awesome Scripts) suite. It is a script designed to automate the process of finding potential privilege escalation vectors on Windows systems.

**Usage:**

* Download the `winpeas.exe` file from the repository.
*   Upload the file to the target system and execute it.

    ```bash
    peas.exe
    ```
* The script will provide detailed output regarding misconfigurations, unquoted service paths, installed applications, and more.

**Link:** [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)

***

### <mark style="color:red;">**Sherlock**</mark>

Sherlock is a PowerShell script that scans for known vulnerabilities in the Windows operating system that can be exploited for privilege escalation.

**Usage:**

* Download `Sherlock.ps1`.
* Run it in a PowerShell session with the command:

```bash
  powershell -ep bypass
  .\\Sherlock.ps1
```

* The script will identify any exploitable vulnerabilities present in the system.

**Link:** [Sherlock](https://github.com/rasta-mouse/Sherlock)

***

### <mark style="color:red;">**Watson**</mark>

Watson is a vulnerability scanner that helps identify vulnerabilities based on the system’s patch level. It’s used to identify missing patches or configurations that could lead to privilege escalation.

**Usage:**

* Download `Watson.exe` or compile it.
* Run the executable in the system:

```bash
Watson.exe
```

**Link:** [Watson](https://github.com/rasta-mouse/Watson)

***

### <mark style="color:red;">**PowerUp**</mark>

PowerUp is part of PowerSploit, a collection of PowerShell scripts that can be used for post-exploitation. PowerUp specifically looks for privilege escalation opportunities on Windows systems.

**Usage:**

* Download `PowerUp.ps1`.
*   Run the script in PowerShell:

    ```bash
    powershell -ep bypass
    .\\PowerUp.ps1
    ```
* PowerUp will scan the system for various weaknesses like service misconfigurations, registry key permissions, etc.

**Link:** [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)

***

### <mark style="color:red;">**Windows Exploit Suggester**</mark>

This tool compares the patch level of the target system against the latest security bulletins from Microsoft to suggest potential privilege escalation vulnerabilities.

**Usage:**

* Clone the repository.
* Update the database:

```bash
  ./windows-exploit-suggester.py --update
```

Use the tool to compare the system info:

```bash
  ./windows-exploit-suggester.py --database <db.xls> --systeminfo <sysinfo.txt>
```

**Link:** [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
