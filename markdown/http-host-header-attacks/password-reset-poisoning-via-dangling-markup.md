# Password reset poisoning via dangling markup

<figure><img src="../../.gitbook/assets/2025-02-22 09_13_40-Burp Suite Professional v2024.9.3 - Temporary Project - Licensed to Zer0DayLab C.png" alt=""><figcaption></figcaption></figure>

***

1️⃣ **Leaving the `<img>` tag open**

* Some websites block malicious JavaScript.
* If you don’t close the `<img>` tag, the browser may auto-complete it incorrectly and execute your script.
*   Example:

    ```
    <img src=x onerror=alert('XSS')
    ```

2️⃣ **Using `?/` in URLs**

* Some websites don’t handle URLs correctly.
* Adding `?/` might trick the website into returning more data than expected.
*   Example:

    ```
    <https://example.com/email?/>
    ```

**This might expose all emails instead of just one!**
