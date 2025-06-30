---
icon: '4'
---

# Mapping the Application

The first step in attacking an application is gathering key information to understand its structure. This involves mapping its content and functionality, identifying visible and hidden features. Once mapped, the focus shifts to analyzing its security mechanisms and technologies (client-side & server-side) to pinpoint potential attack surfaces. This chapter covers practical steps, techniques, and tools to enhance application mapping efficiency.

***

*   Enumerating Content and Functionality

    Most application content and functionality can be found through manual browsing by following links and navigating through features like registration or password resets. A **site map**, if available, can be a useful reference. However, for a thorough inspection and complete record, more advanced techniques beyond simple browsing are needed.

***

*   Web Spidering

    Web spidering automates the process of discovering web application content by following links and submitting forms. Advanced spiders, like **Burp Spider**, can analyze **JavaScript, forms, and POST requests** to uncover hidden pages. However, automated spidering has limitations, such as **failing to handle JavaScript menus, breaking authentication, or missing content due to unique URL structures**. Additionally, running a spider can be **risky** if the application contains sensitive admin functions that can be triggered accidentally.

***

* User-Directed Spidering
  * The user manually explores the application while using a proxy tool (Burp Suite, WebScarab) to track requests and responses.
  * **Advantages:**
    * Handles complex navigation mechanisms.
    * Ensures valid input for forms.
    * Maintains authenticated sessions.
    * Identifies sensitive functions without triggering them.
  * **Implementation Steps:**
    1. Configure your browser to use Burp or WebScarab as a proxy.
    2. Manually browse the entire application.
    3. Review the generated site map for any missed content.
    4. Exclude risky URLs before running automated spidering.
    5. Use the gathered data to find vulnerabilities.

***

* Discovering Hidden Content
  * Some web applications contain **hidden functionality** used for testing or debugging.
  * Different user roles may have access to different features, which attackers can exploit.
  * **Types of hidden content:**
    * Backup files (`.bak`, `.old`, etc.).
    * Archives containing sensitive data.
    * New but unlinked features.
    * Old vulnerable versions of files.
    * Configuration files (DB credentials, API keys).
    * Source code files.
    * Log files containing usernames, session tokens, and URLs.
  * **Discovery methods:** Combine automated tools and manual exploration.

***

*   Brute-Force Techniques

    Brute-force techniques are used to **guess hidden pages and directories** in a web application by sending a huge number of requests automatically. This helps in discovering **admin panels, backup files, configuration files, and other sensitive content.**

    #### **How Does It Work?**

    📌 **1. Collect Initial Data**

    *   First, you manually browse the website or use **Burp Suite** to map known pages like:

        ```

        <https://site.com/login.php>
        <https://site.com/home/account.php>
        <https://site.com/register.php>
        ```
    *   Based on this, you can guess other pages like:

        ```

        <https://site.com/admin.php>
        <https://site.com/config.php>
        <https://site.com/backup/📌> **2. Try Common Directory and File Names**
        ```
    *   Test for **common admin directories:**

        ```

        <https://site.com/admin/>
        <https://site.com/backup/>
        <https://site.com/private/>

        ```
    *   Then check for **possible hidden pages inside them:**

        ```
        <https://site.com/admin/login.php>
        <https://site.com/admin/dashboard.php>

        ```

    📌 **3. Analyze HTTP Response Codes**

    * Websites return different **HTTP status codes**, which help identify valid resources:
      * **200 OK** ✅ → Page exists
      * 400 Bad Request – The application may use a custom naming scheme for directories and files within URLs, which a particular request has not complied with. More likely, however, is that the wordlist you are using contains some whitespace characters or other invalid syntax
      * **302 Found** 🔄 → Redirects (often to a login page)
      * **403 Forbidden** ❌ → Page exists but access is restricted
      * **500 Internal Server Error** ⚠ → The server crashed (possibly due to missing parameters)

    📌 **4. Use Burp Suite, Dirb, or Gobuster for Automation**

    * **Burp Intruder** can send thousands of requests based on a **wordlist** of common filenames.
    * **SecLists** and other **wordlists** help in **guessing typical file and folder names.**

    📌 **5. Review JavaScript and Source Code for Hints**

    *   Sometimes, JavaScript or HTML comments reveal hidden content:

        ```html
        html
        CopyEdit
        <!-- TODO: Secure admin panel at /admin123 -->

        ```
    *   Look for **hidden files** like:These may contain **database credentials or other sensitive data.**

        ```
        pgsql
        CopyEdit
        .bak, .old, .inc, .log, .DS_Store

        ```

    📌 **6. Repeat and Expand the Search**

    * Every time you discover **a new directory or file**, use it as a starting point for deeper testing.
    * Example: If `EditUser.php` exists, try guessing `DeleteUser.php`, `AddUser.php`, etc.

    #### **Summary:**

    🔍 **Brute-force helps find hidden pages and files.**

    🛠️ **Use tools like Burp Suite, dirb, and gobuster for automation.**

    📡 **HTTP response codes help identify valid resources.**

    📑 **Check JavaScript and other files for hidden clues.**

    ♻ **Keep expanding your search based on discovered content**

***

*   Inference from Published Content

    Most web applications follow **specific naming patterns** for their files and directories. By analyzing existing pages, we can **predict and discover hidde content** more effectively.

    #### **How Does It Work?**

    📌 **1. Review Already Discovered Content**

    * Check the **directories, files, and extensions** found through **brute-force or manual browsing.**
    *   Example: If you find these pages:You can guess:

        ```
        CopyEdit
        AddDocument.jsp
        ViewDocument.jsp

        ```

        ```

        EditDocument.jsp
        RemoveDocument.jsp

        ```

    📌 **2. Identify Naming Patterns**

    * Developers follow different styles when naming files:
      * **Verbose:** `AddANewUser.asp`
      * **Short:** `AddUser.asp`
      * **Abbreviated:** `AddUsr.asp`
      * **Cryptic:** `AddU.asp`
    * Recognizing their style **helps in guessing other file names.**

    📌 **3. Look for Numbered or Dated Files**

    *   If a company has:You can **predict** the next file:

        ```
        AnnualReport2022.pdf
        AnnualReport2023.pdf
        ```

        ```
        AnnualReport2024.pdf
        ```
    * Many companies accidentally **upload confidential reports early,** making them discoverable.

    📌 **4. Analyze JavaScript and HTML for Clues**

    *   **Check comments in the source code** for hidden pages or API endpoints:

        ```html
        html
        CopyEdit
        <!-- Admin panel under development: /admin_secret.php -->
        ```
    * **Look for disabled buttons or forms** that might hint at hidden functions.
    * **Java applets and ActiveX controls** may contain **sensitive data** like database credentials.

    📌 **5. Search for Backup and Temp Files**

    *   Developers often leave behind **backup or temporary files:**

        ```

        config.bak
        database.old
        index.php~

        ```
    * Tese files can **reveal passwords, database credentials, or source code.**

    📌 **6. Automate More Smartly**

    *   Use **Burp Intruder, dirb, or gobuster** to test different combinations:

        ```
        CopyEdit
        AddUser.jsp
        EditUser.jsp
        DeleteUser.jsp
        ```
    * **If a consistent pattern is found,** generate wordlists based on it.

    📌 **7. Repeat the Process for More Discoveries**

    * Every time you find **a new directory or file,** use it as a **starting point** for further discovery.
    *   Example: If `ManageOrders.jsp` exists, you can try:

        ```
        CopyEdit
        AddOrder.jsp
        DeleteOrder.jsp
        ```

    #### **Summary:**

    🔍 **Look at existing pages to predict hidden ones.**

    🛠️ **Check JavaScript, HTML comments, and backup files for hints.**

    📡 **Use wordlists to automate smartly based on naming patterns.**

    ♻ **Repeat the process as new content is discovered.**

***

*   Use of Public Information

    Sometimes an application might have **hidden content** that isn't linked anymore, but still exists online in **cached pages** or **archives**. We can make use of **public resources** like **search engines** and **web archives** to discover such content.

    #### **Important Public Resources**

    1. **Search Engines** (like Google, Yahoo, MSN):
       * Search engines maintain a **cache** of web pages. Even if pages are removed from the site, these cached copies still exist.
       * They also **index** content, meaning they know about all the pages of a site, even the ones not linked anywhere.
    2. **Web Archives** (like the **WayBack Machine** at [web.archive.org](http://web.archive.org/)):
       * These tools archive websites and keep **historical snapshots**. You can go back in time and see what a website looked like on a specific date.
       * This is useful to find content that is no longer live but was once accessible.

    #### **HACK STEPS** (Steps to find hidden content)

    #### **1. Use search engines**

    *   Run searches using different search engines and web archives. Here's how you can search:

        🔍 **Google Queries**:
    * `site:www.target.com`: Shows all the pages of the site that Google has indexed.
    * `site:www.target.com login`: Shows pages that contain "login," useful for finding login pages, password resets, and admin menus.
    * `link:www.target.com`: Shows external pages linking to the target site, including old content or partner links.
    * `related:www.target.com`: Finds pages similar to the target site, often leading to discussions or third-party references.
    * Explore **Google Groups** and **Google News** too for possible extra results.
    * Don’t forget to check **cached versions** of interesting pages. This may show you content that is **no longer available** on the live site.

    #### **2. Dive into web archives**

    * Use web archives like the **WayBack Machine** to see how the site looked in the past. This may show you pages or functions that are no longer accessible on the site.

    #### **3. Search multiple domain names**

    * If the target site uses **multiple domains** (e.g., [company.com](http://company.com), [support.company.com](http://support.company.com)), search for information across all those domains.

    #### **4. Identify old, vulnerable content**

    * Sometimes, **old, unused features** might still be available, and they may have **security vulnerabilities** that no longer exist in the current version of the app.

    #### **5. Look into forums for developer posts**

    * Developers often discuss software issues on forums. Searching for **developer names** or **project details** can reveal useful information such as:
      * Technologies used in the application
      * Known bugs
      * Source code snippets
      * **Security issues** the developers encountered

***

*   Leveraging the Web Server

    Web server vulnerabilities can reveal **hidden content and functionality** not directly linked within the application. These flaws allow attackers to gather sensitive information or identify weak points.

    **How Web Server Vulnerabilities Help Attackers**

    1. **Directory Listing**:
       * Misconfigured servers may expose **file and folder lists**.
    2. **Source Code Disclosure**:
       * Some bugs allow access to **raw source code** of dynamic pages (like PHP or ASPX).
    3. **Default Content**:
       * Servers may have **test scripts or old diagnostic files** that contain **known vulnerabilities**.
    4. **Third-Party Components**:
       * Web apps often use **pre-built components** (like shopping carts or email integrations), which can have **security flaws** if outdated.

    **Using Nikto to Scan Web Servers**

    **Nikto** is a security tool used to discover vulnerabilities by checking for:

    * **Hidden files or old directories**.
    * **Sample scripts or outdated components**.
    * **Common misconfigurations**.

    **Nikto Command Example**:

    ```bash
    bash
    CopyEdit
    perl nikto.pl -h 127.0.0.1

    ```

    **Sample Output**:

    ```
    bash
    CopyEdit
    + /bin/ - This might be interesting... (GET)
    + /client/ - This might be interesting... (GET)
    + /oracle - Redirects to /oracle/, This might be interesting...
    + /cgi-bin/login.pl - This might be interesting... (GET)

    ```

    **Nikto Options**

    * **Custom directories**: Use `-root /cgi/` if files are in nonstandard locations.
    * **Custom 404 pages**: If the site uses a custom error page, use `404` to identify it.
    * **Manual Verification**: Always **double-check results**—Nikto can produce **false positives**.

    #### **In Short:**

    🛠️ **Nikto** helps find **hidden pages, misconfigurations, and outdated components** on web servers.

    🚀 **Attackers use web server bugs** to list directories, read source code, and spot security gaps.

    ✔️ **Always verify results manually** to confirm real vulnerabilities.

    #### **Discovering Hidden Parameters in Requests**

    **The Issue:**

    Some applications accept **hidden parameters** that significantly alter behavior. For example, adding `debug=true` might **disable security checks**, **bypass access controls**, or **leak sensitive debugging data**. These parameters are often not visible in URLs or forms, making them hard to detect.

    #### **Security Testing Steps:**

    1. **Test Debug Parameters:**
       * Try **common parameter names** (`debug, test, hide, source`).
       * Use **different values** (`true, yes, on, 1`).
       * Add parameters to:
         *   **URL Query String:**

             ```
             bash
             CopyEdit
             <https://example.com/page?debug=true>

             ```
         *   **POST Body:**

             ```
             pgsql
             CopyEdit
             POST /login HTTP/1.1
             Host: example.com
             Content-Length: 50

             username=admin&password=1234&debug=true

             ```
    2. **Use Burp Intruder:**
       * Perform **Cluster Bomb attacks** to test various name-value combinations.
       * Target **critical pages** (login, search, file upload/download).
    3. **Analyze Responses:**
       * Look for **unexpected changes**, such as:
         * **Extra information displayed** (`Stack Trace, Error Details`)
         * **Bypassed security restrictions** (`Access Granted`)
         * **Format differences** (`Debug Mode Enabled`).

***

*   Application Analysis

    **Goal:**

    Mapping content alone is not enough; you must understand **how the application works**, **technologies used**, and **potential attack surfaces** to identify exploitable vulnerabilities.

    **Key Areas to Investigate:**

    1. **Core functionality** – What can the application do under normal conditions?
    2. **Secondary behaviors** – External links, error messages, logging functions, redirects.
    3. **Security mechanisms** – How sessions, access controls, and authentication work.
    4. **User input locations** – URL parameters, POST data, cookies, and query strings.
    5. **Client-side technologies** – Forms, JavaScript, Java Applets, ActiveX, Flash.
    6. **Server-side technologies** – Static vs. dynamic pages, SSL, web servers, databases.
    7. **Internal application structure** – How backend logic and responses reveal hidden details.

    #### **Identifying Entry Points for User Input**

    When testing a web application, you need to find all the places where the app takes input from users and sends it to the server. These are called **entry points**. Here’s where to look:

    1. **URLs**: Everything in the URL up to the `?` (query string marker).
    2. **Query Strings**: Parameters after the `?` in the URL (e.g., `?id=123`).
    3. **POST Request Body**: Data sent in the body of a POST request (e.g., form submissions).
    4. **Cookies**: Data stored in cookies.
    5. **HTTP Headers**: Sometimes headers like `User-Agent`, `Referer`, `Accept`, `Accept-Language`, and `Host` are processed by the app.

    **Non-Standard Query Strings**

    Some apps use custom formats for query strings instead of the standard `?param=value&param2=value2`. Examples:

    * `/dir/file;foo=bar&foo2=bar2`
    * `/dir/file?foo=bar$foo2=bar2`
    * `/dir/file?data=<foo>bar</foo><foo2>bar2</foo2>`

    If the app uses non-standard formats, you need to adjust your testing to account for these. For example, if the query string contains embedded XML, you should test within the XML fields for vulnerabilities like SQL injection.

    **Out-of-Band Entry Points**

    Some apps take user input from places outside the usual HTTP traffic. These are harder to detect and require understanding the app’s functionality. Examples:

    * A **webmail app** that processes emails received via SMTP.
    * A **publishing app** that fetches content from another server via HTTP.
    * An **intrusion detection app** that collects data using a network sniffer and displays it in a web interface.

    **Key Takeaways**

    1. Look for user input in **URLs**, **query strings**, **POST bodies**, **cookies**, and **HTTP headers**.
    2. Watch out for **non-standard query string formats** and test them carefully.
    3. Some apps take input from **out-of-band channels** (like emails or external servers), so think beyond just HTTP traffic.

***

*   Identifying Entry Points for User Input

    **Goal:**

    To understand how user data is processed, identify all possible ways users can send data to the server.

    #### **Key Entry Points:**

    1. **URLs** – Before the `?` marker.
    2. **Query string parameters** – Data in `?param=value&param2=value2`.
    3. **POST request body parameters** – Form data or login credentials.
    4. **Cookies** – Used for sessions and user permissions.
    5. **HTTP headers** – Some apps process headers like `User-Agent`, `Referer`, `Accept`, etc.

    #### **Non-Standard Query String Formats**

    Some applications use **custom query formats**, e.g.:

    * `/dir/file;foo=bar&foo2=bar2`
    * `/dir/file?foo=bar$foo2=bar2`
    * `/dir/foo.bar/file`

    🔍 **Ignoring these formats can lead to missing vulnerabilities like SQL Injection or Path Traversal.**

    #### **Out-of-Band Input Channels**

    Some applications process user-controlled data **outside normal HTTP requests**, e.g.:

    1. **Webmail apps** processing SMTP emails.
    2. **Content retrieval apps** fetching remote HTTP content.
    3. **IDS apps** capturing network traffic and displaying it via a web UI.

    📌 **These require deeper context understanding to detect security risks.**

***

*   Identifying Server-Side Technologies

    It is normally possible to fingerprint the technologies employed on the server via various clues and indicators

***

*   Banner Grabbing

    **🔍 Banner Grabbing:**

    Extracting server version details from **HTTP headers**, **HTML templates**, **custom headers**, and **URL query strings**.

    **🛠 Methods:**

    * Telnet: `telnet target.com 80`
    * Curl: `curl -I <http://target.com`>
    * Nmap: `nmap -p 80 --script http-server-header target.com`

    **🚀 Prevention:**

    * Hide server banners (`ServerTokens Prod` in Apache, `server_tokens off;` in Nginx).
    * Use a **WAF**.
    * **Keep software updated**.

***

*   HTTP Fingerprinting

    **🔍 HTTP Fingerprinting:**

    Even if a server hides its `Server` header, its behavior (error handling, HTTP responses) can reveal its type.

    **🛠 Detection Methods:**

    1️⃣ **Analyze HTTP responses** (custom headers, error handling).

    2️⃣ **Observe server behavior** (deviation from standards).

    3️⃣ **Use tools** like **Httprint, Nmap, WhatWeb**.

    **📂 File Extensions & Technologies:**

    * `.asp` → **ASP**, `.php` → **PHP**, `.jsp` → **JSP**, `.py` → **Python**, `.dll` → **Compiled C/C++**

    **🚀 Prevention:**

    * Hide headers, disable verbose errors, use a **WAF**, and rewrite URLs.

***

*   Directory Names

    **🔍 Directory Names:**

    Subdirectory names can reveal the technologies or platforms in use on the server. For example:

    | **directory**                    | **technology**                               |
    | -------------------------------- | -------------------------------------------- |
    | `servlet`                        | **Java Servlets**                            |
    | `pls`                            | **Oracle Application Server PL/SQL Gateway** |
    | `cfdocs` or `cfide`              | **Cold Fusion**                              |
    | `SilverStream`                   | **SilverStream Web Server**                  |
    | `WebObjects` or `{function}.woa` | **Apple WebObjects**                         |
    | `rails`                          | **Ruby on Rails**                            |

***

*   Session Tokens

    **🔍 Session Tokens:**

    Many web servers and platforms generate session tokens with names that indicate the underlying technology. For example:

    | **token name**      | **technology used**                         |
    | ------------------- | ------------------------------------------- |
    | `JSESSIONID`        | **Java Platform**                           |
    | `ASPSESSIONID`      | **Microsoft IIS Server**                    |
    | `ASP.NET_SessionId` | **Microsoft** [**ASP.NET**](http://asp.net) |
    | `CFID` / `CFTOKEN`  | **Cold Fusion**                             |
    | `PHPSESSID`         | **PHP**                                     |

***

* Mapping the Attack Surface
  * Client-side validation — Checks may not be replicated on the server.
  * Database interaction — SQL injection.
  * File uploading and downloading — Path traversal vulnerabilities.
  * Display of user-supplied data — Cross-site scripting.
  * Dynamic redirects — Redirection and header injection attacks.
  * Login — Username enumeration, weak passwords, ability to use brute force.
  * Multistage login — Logic flaws.
  * Session state — Predictable tokens, insecure handling of tokens.
  * Access controls — Horizontal and vertical privilege escalation.
  * User impersonation functions — Privilege escalation.
  * Use of cleartext communications — Session hijacking, capture of credentials and other sensitive data.
  * Off-site links — Leakage of query string parameters in the Referer header.
  * Interfaces to external systems — Shortcuts in handling of sessions and/or access controls.
  * Error messages — Information leakage.
  * Email interaction — Email and/or command injection.
  * Native code components or interaction — Buffer overflows.
  * Use of third-party application components — Known vulnerabilities.
  * Identifiable web server software — Common configuration weaknesses, known software bugs.

***

*   Summary

    **Mapping the application** is essential before attacking it. While jumping straight into probing for bugs may seem tempting, taking time to understand the application's functionality, technologies, and attack surface will be beneficial in the long run.

    **Core methodology involves:**

    1. **Manual browsing and user-directed spidering** to enumerate visible content and functionality.
    2. **Brute force combined with human inference** to discover hidden content.
    3. **Intelligent analysis of the application** to identify key functionality, behavior, security mechanisms, and technologies.
    4. **Assessing the attack surface** to focus on the most promising functions for further probing into exploitable vulnerabilities.
