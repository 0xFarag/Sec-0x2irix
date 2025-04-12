# 3️⃣ Web Application Technologies

### HTTP Method

**1. TRACE:**

* **Purpose:** A diagnostic method that returns the exact message that the server received. It helps to detect any manipulation or changes made to the request by intermediaries (like proxies).

**Example:**

```
http
CopyEdit
TRACE /index.html HTTP/1.1
Host: example.com

```

The server responds:

```
http
CopyEdit
HTTP/1.1 200 OK
Date: Sat, 25 Jan 2025 10:00:00 GMT
Content-Length: 39
Content-Type: message/http

TRACE /index.html HTTP/1.1
Host: example.com

```

**Usage in attacks:** Can be exploited to find hidden changes or data manipulation by proxies. It may also be used to attack other application users.

***

**2. OPTIONS:**

* **Purpose:** Asks the server which HTTP methods are allowed for a specific resource. The server responds with an `Allow` header.

Example:

```
http
CopyEdit
OPTIONS /index.html HTTP/1.1
Host: example.com

```

The server responds:

```
http
CopyEdit
HTTP/1.1 200 OK
Allow: GET, POST, OPTIONS, HEAD

```

**Usage in attacks:** An attacker can discover methods like **PUT** or **DELETE**, which can be dangerous if improperly configured.

***

**3. PUT:**

* **Purpose:** Allows the client to upload a resource to the server. If enabled, it can be used to upload malicious scripts or files to the server.

**Example:**

```
http
CopyEdit
PUT /uploads/malicious_script.php HTTP/1.1
Host: example.com
Content-Type: application/x-php
Content-Length: 123

<?php echo "Hacked"; ?>
```

**Usage in attacks:** Can be exploited to upload harmful scripts if the server does not restrict it properly.

***

**4. HEAD:**

* **Purpose:** Like **GET**, but only retrieves headers, not the body of the resource. Useful to check if a resource exists before requesting it fully.

**Example:**

```
http
CopyEdit
HEAD /index.html HTTP/1.1
Host: example.com

```

The server responds:

```
http
CopyEdit
HTTP/1.1 200 OK
Date: Sat, 25 Jan 2025 10:00:00 GMT
Content-Type: text/html
Content-Length: 234

```

**Usage in attacks:** Often used in reconnaissance to get information about the server, like the type and length of content.

***

#### **Summary:**

* **TRACE:** Returns the exact message received. Useful for proxy issues.
* **OPTIONS:** Asks the server which methods are allowed. Can find dangerous methods like **PUT**.
* **PUT:** Uploads files to the server. Can be used to upload malicious scripts.
* **HEAD:** Retrieves headers only. Useful for checking resource info without loading content.

***

#### Exploit For Method

**1. TRACE - Cross-Site Tracing (XST):**

* **Description:** This vulnerability allows attackers to use the **TRACE** method to gather sensitive information, such as data manipulated by proxies or the server itself. If the server allows **TRACE** requests, attackers can send requests to see if there’s any manipulation of the request.
* **Exploitation:** Attackers can gain access to sensitive data such as cookies or HTTP headers that pass through proxies.

***

**2. OPTIONS - Verb Tampering:**

* **Description:** This vulnerability leverages the **OPTIONS** request to discover the available HTTP methods on the server. If the server exposes methods like **PUT** or **DELETE**, attackers can leverage these methods to perform unauthorized actions.
* **Exploitation:** If the server allows dangerous methods like **PUT** or **DELETE**, an attacker could upload arbitrary files (e.g., malicious scripts) or delete important resources.

***

**3. PUT - Arbitrary File Upload:**

* **Description:** If the server allows **PUT** requests without proper authentication or validation, attackers can upload malicious files, such as PHP scripts or other types of harmful content.
* **Exploitation:** An attacker could upload a malicious PHP script and execute it on the server, potentially leading to remote code execution or further attacks.

***

**4. HEAD - Information Disclosure:**

* **Description:** **HEAD** requests can be exploited to gather sensitive information from the server, such as content type, message length, and other details in the HTTP headers. If the server reveals too much information, it can aid attackers in planning an attack.
* **Exploitation:** An attacker can use **HEAD** requests to gather server details and plan further attacks based on the information they retrieve.

***

**5. PUT + OPTIONS - PUT Method Enabled:**

* **Description:** If the **OPTIONS** method is enabled and the **PUT** method is available, attackers can upload arbitrary files to the server.
* **Exploitation:** For example, if **PUT** is enabled, an attacker could upload a PHP script or JavaScript file to execute malicious actions on the server.

***

**Summary:**

* **TRACE:** Can lead to **Cross-Site Tracing (XST)** attacks, exposing sensitive information.
* **OPTIONS:** Can lead to **Verb Tampering**, revealing unsafe methods like **PUT** or **DELETE**.
* **PUT:** Can enable **Arbitrary File Upload**, allowing attackers to upload malicious scripts.
* **HEAD:** Can lead to **Information Disclosure**, where attackers gather useful details about the server.

***

### URL

```
protocol://hostname[:port]/[path/]file[?param=value]
<http://wahh-app.com/books/search.asp?q=wahh>
```

The proper term for a **URL** is **URI (Uniform Resource Identifier)**, but "URL" is more commonly used.

***

### General Headers

**Connection**

* **What it does**: Controls whether the TCP connection should stay open after completing the HTTP request/response.
* **Example**: `Connection: keep-alive` (keeps the connection open for more requests).
* **Why it matters**: Saves time/resources if multiple requests are being sent between client and server.

**General Headers** → connection , content-Encoding, content-Length, content-type, transfer-encoding(show how data can be transfer ex. chunked )

***

### Response Headers

* **Cache-Control**: Tells the browser whether to "store this thing or not."
* **ETag**: Provides a unique identifier for a version of the resource (Entity Tag) `ETag: "abc123"`
* **Expires**: Specifies the "last valid time" for the stored resource.
* **Location:** Indicates the new URL for redirection (used with **3xx** status codes).
* **Pragma**: Similar to Cache-Control but for older systems.
* **Server**: Reveals the type of server the website is running on.
* **Set-Cookie**: The server gives the browser a small "cookie" to maintain the session or specific information.
*   **WWW-Authenticate**: Provides authentication details when the client receives a **401 Unauthorized** status.

    ```
    WWW-Authenticate: Basic realm="Secure Area"
    ```

***

### Cookies

**`HttpOnly`** : Prevents the cookie from being accessed by **client-side JavaScript** which can help reduce the risk of XSS (Cross-Site Scripting) attacks.

**Cookie Security Settings**: Always set:

* `HttpOnly` to block JavaScript access.
* `Secure` for HTTPS-only cookies.
* `SameSite` to control cross-origin cookie behavior and prevent CSRF (Cross-Site Request Forgery).

***

### Status code

* **1xx — Informational**:
  * **100 Continue**: The server acknowledges the request headers and asks the client to proceed with sending the request body. • Commonly used with `Expect: 100-continue` in the request headers.
* **2xx — Success**:
  * **200 OK**: The request was successful, and the response contains the requested data.
  * **201 Created**: The request (usually a PUT) was successful, and a new resource was created.
* **3xx — Redirection**:
  * **301 Moved Permanently**: The resource has been permanently moved to a new URL.
  * **302 Found**: The resource is temporarily available at a different URL.
  * **304 Not Modified**: The client can use its cached version of the resource, as it hasn’t changed.
* **4xx — Client Errors**:
  * **400 Bad Request**: The request is invalid or malformed.
  * **401 Unauthorized**: Authentication is required to access the resource.
  * **403 Forbidden**: Access to the resource is denied, regardless of authentication.
  * **404 Not Found**: The requested resource does not exist.
  * **405 Method Not Allowed**: The HTTP method used is not supported for the resource.
  * **413 Request Entity Too Large**: The request body exceeds the server’s size limit.
  * **414 Request URI Too Long**: The URL exceeds the server’s length limit.
* **5xx — Server Errors**:
  * **500 Internal Server Error**: The server encountered an unexpected error while processing the request.
  * **503 Service Unavailable**: The server is operational, but the application or service is temporarily unavailable.
