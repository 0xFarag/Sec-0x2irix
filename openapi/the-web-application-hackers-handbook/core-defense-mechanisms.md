---
icon: '2'
---

# Core Defense Mechanisms

### Core elements for defense mechanism for web App :

1. Handel user access
2. Handel user input
3. Handel attackers → take suitable defense against attacker
4. Managing the application itself, by enabling administrators to monitor its activities and configure its functionality.

***

#### **Handel user access**

first to know categories for each user (anonymous user, authenticated user, administrative user) because each of type have a different privilege to access data.

**can be handled :**

■ Authentication ■ Session management ■ Access control

***

#### Handel user input

suppose all user inputs is malicious and must be filtered

■ Varieties of Input → make a different rule for each type of data like (login form, address, blog) each of one have a different cases

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**How to Input Handling:**

*   **Reject Known Bad** → check if this data have any type know as a dangerous

    but hackers can manipulate data by encoding or any techniques . known as a `black list`
* **Accept Known Good** → known as a `white list`
* **sanitization** → clear inputs from any malicious code - `<script>` transfer to any word we can’t execute like `&lt;script&gt;`
*   **Safe Data Handling** →Focus to how App process this data not validate for this data . we use `Parameterized Queries` \*\*\*\*to secure app from SQLI injection

    ```php
    <?php
    try {
        // الاتصال بقاعدة البيانات
        $dsn = "mysql:host=localhost;dbname=testdb;charset=utf8mb4";
        $username = "root";
        $password = "password";

        // إنشاء اتصال PDO
        $pdo = new PDO($dsn, $username, $password);

        // ضبط PDO ليظهر الأخطاء
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        echo "Connected successfully!";
    } catch (PDOException $e) {
        echo "Connection failed: " . $e->getMessage();
    }
    ?>

    ```
*   **Semantic Checks** → بيتحقق من معني البيانات و السياق اللي جات فيه بمعني انك لو مثلا بتعمل عمليه تحويل فلوس من حساب لحساب وفي برامتر في رقم الحساب فلو غيرته لارقام فالعادي دا كدا البيانات شكلها صحيح (رقم الحساب هو أرقام فقط، مفيش مشاكل ظاهرية).

    لكن الرقم مش بتاع المستخدم الحالي، وده استغلال للهجوم

    How Work ?

    ```php
    <?php
    // فرضاً المستخدم الحالي رقمه 123456
    $currentUserAccount = 123456;

    // رقم الحساب اللي المستخدم بعت بياناته
    $submittedAccount = $_POST['account_number'];

    // التحقق من إن الحساب المرسل هو نفس الحساب المسجل
    if ($currentUserAccount !== $submittedAccount) {
        die("Unauthorized access!");
    }

    // استكمال العملية بأمان
    echo "Transaction allowed!";
    ?>

    ```
* **Boundary Validation**→ like checking your bag at different checkpoints before entering a place. At each checkpoint, the data (like a user’s info) is checked to make sure it’s safe. For example, when someone logs in, the username and password are checked for mistakes first, then they’re safely used in a database search, then passed to other services without causing issues, and finally shown on the screen in a secure way. This helps stop problems like hacking or breaking the app.

<figure><img src="../../.gitbook/assets/image 1 (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Multistep Validation and Canonicalization** → When you receive data from a user, there might be steps to filter or sanitize it. But if you're cleaning things up in multiple steps, sometimes attackers can slip malicious data between those steps if you're not careful. For example, if you try to block `<script>` tags, an attacker might sneak them in as `<scr<script>ipt>`. Also, if you're trying to block certain characters like quotation marks, they might use encoding tricks like `%27` to bypass your checks.

***

### Handel attacker

* **Handling errors** → application must handle error without any message for user to how can solve this error like:

<figure><img src="../../.gitbook/assets/image 2 (2) (1).png" alt=""><figcaption></figcaption></figure>

*   **Maintaining audit logs** → help owners to understand what happen exactly what has taken place, which vulnerabilities (if any) were exploited

    * All events relating to the authentication functionality, such as successful and failed login, and change of password.
    * Key transactions, such as credit card payments and funds transfers.
    * Access attempts that are blocked by the access control mechanisms.
    * Any requests containing known attack strings that indicate overtly malicious intentions.

    How to log securely:

    * Record details like:
      * **Time of the event.**
      * **IP address** of the request.
      * **Session token.**
      * **User account** (if logged in).

    Protect the logs so attackers can’t read or modify them.

    * Best practice? Store logs on a separate system that only accepts updates from the application. For extra security, save logs on write-once media to prevent tampering, even in case of a breach.

<figure><img src="../../.gitbook/assets/image 3 (2).png" alt=""><figcaption></figcaption></figure>

*   **Alerting administrators** → When someone tries to attack your application, it’s not enough to just log what happened and investigate later. Sometimes, you need to act immediately, like blocking the attacker's IP, disabling their account, or even shutting down the app temporarily if it’s a serious attack. But the most important thing is that the alerts sent to the administrators (admins) should be accurate. You need to notify them about real attacks without overwhelming them with too many unnecessary alerts.

    Signs of an attack you should watch for:

    1. **Unusual activity:** Like receiving a large number of requests from the same IP or user account (could be a bot or script attack).
    2. **Business anomalies:** For example, an unusually high number of fund transfers to/from a single account.
    3. **Requests with known attack strings.**
    4. **Modifications to hidden or protected data:** Like changing an account number in a hidden field.

    Solutions:

    * Tools like web application firewalls (WAFs) can help detect and block attacks or send alerts. But these tools are general-purpose and might miss more advanced attacks that exploit your app’s specific logic.
    * **The best approach?** Integrate alerting directly into your app’s logic. For example:
      * If a cookie has unexpected values -> send an alert.
      * If a user changes an account number in a hidden field -> send an alert.
*   **Reacting to attacks** → When an attacker starts probing your application for vulnerabilities, they often send many suspicious or malicious requests. Your app should block as much of this as possible with strong input validation, but let’s be real: no system is perfect, and some vulnerabilities might still exist.

    That’s why some apps have **defensive reaction mechanisms** to slow attackers down, like:

    * Making the app respond slower to their requests.
    * Forcing them to log back in or verify themselves before continuing.

    While these won’t stop the most determined hackers, they can frustrate casual attackers and buy you time to investigate and take stronger actions.

***

#### Managing the Application

Your application’s admin panel is like the "control room" for managing user accounts, roles, logs, and settings. While it’s essential, it’s also a big target for attackers because it holds all the keys to the kingdom.

**Why Attackers Love Admin Panels:**

1. **Privilege Escalation:**
   * Weak login mechanisms might let an attacker sneak in as an admin.
   * Some apps don’t secure admin actions properly, allowing attackers to create powerful accounts.
2. **Cross-Site Scripting (XSS):**
   * If there’s XSS in the admin panel, an attacker can hijack the admin’s powerful session.
3. **Poor Security Testing:**
   * Admin areas are often less tested because admins are considered “trusted.”
   * They perform risky tasks (like accessing server files), making them dangerous if hacked.

<figure><img src="../../.gitbook/assets/image 4 (2).png" alt=""><figcaption></figcaption></figure>

***

### Summary

Almost all web applications use similar **core security mechanisms** to protect against attackers. These mechanisms are both the primary defense and the main target for attackers. Most vulnerabilities happen because of flaws in these mechanisms.

The **key focus areas** for security:

1. **Handling User Access:**
   * How users log in, authenticate, and manage their sessions.
2. **Handling User Input:**
   * Validating and sanitizing what users submit to prevent injection attacks.

If there are weaknesses here, attackers can:

* Steal other users’ data.
* Perform unauthorized actions.
* Inject malicious code or commands.
