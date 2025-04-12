---
cover: ../../.gitbook/assets/wallpaperflare.com_wallpaper 1(1).jpg
coverY: 0
---

# HTTP Host header attacks

**What’s the HTTP Host Header?**

The **HTTP Host header** is a crucial part of HTTP requests, particularly in HTTP/1.1. It specifies the domain name of the server to which the client (e.g., a web browser) is trying to connect. This header is essential because it allows multiple websites (or domains) to be hosted on the same IP address, a feature known as **virtual hosting**.

ex: When you visit a website like `https://www.example.com`, your browser sends an HTTP request that includes the `Host` header:

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html
```

Host header altered when the request has been forwarded by an intermediary system this value altered before arrive to back-end

***

**What is the purpose of the HTTP Host header?**

help identify which back-end component the client wants to communicate with it. • **Virtual Hosting**: When multiple websites share the same IP address, the server uses the `Host` header to determine which website the client is requesting. • **Routing Requests**: The server uses the `Host` header to route the request to the correct website or [application.in](http://application.in) cases where clients access the website via a content delivery network (CDN).

***

**How does the HTTP Host header solve this problem?**

When a browser sends the request, the target URL will resolve to the IP address of a particular server. When this server receives the request, it refers to the Host header to determine the intended back-end and forwards the request accordingly.

***

```html
<a href="https://_SERVER['HOST']/support">Contact support</a>
```

**Vulnerability can be occur**

* Web cache poisoning
* Business logic flaws in specific functionality
* Routing-based SSRF
* Classic server-side vulnerabilities, such as SQL injection

***

**Exploiting HTTP Host header vulnerabilities**

[Labs](password-reset-poisoning-via-dangling-markup.md)

***

**How to prevent HTTP Host header attacks**

*   **Avoid using the Host header** in server-side code whenever possible. Use relative URLs instead to prevent web cache poisoning.

    Relative URL

    ```html
    <a href="/dashboard">Go to Dashboard</a>
    ```

    Absolute URL

    ```html
    <a href="<https://example.com/dashboard>">Go to Dashboard</a>
    ```
*   **Protect absolute URLs** by specifying the domain manually in a configuration file instead of relying on the Host header.

    ```
    # settings file
    BASE_URL = "<https://example.com>"

    # when creating url
    reset_link = f"{BASE_URL}/reset-password"

    ```
*   **Validate the Host header** by checking it against a whitelist of allowed domains to reduce exposure to Host header injection attacks.

    Ex: Django

    ```python
    ALLOWED_HOSTS = ["example.com", "sub.example.com"]
    ```

    Express.js (Node.js)

    ```jsx
    const allowedHosts = ["example.com", "sub.example.com"];

    app.use((req, res, next) => {
      if (!allowedHosts.includes(req.hostname)) {
        return res.status(403).send("Forbidden");
      }
      next();
    });

    ```
*   **Don't support Host override headers** like `X-Forwarded-Host`, as they can be used in attacks.

    in Nginx to disable this option

    ```python
    server {
        set $host "";
        if ($http_x_forwarded_host) {
            return 400;
        }
    }
    ```
* **Whitelist permitted domains** in load balancers and reverse proxies to prevent routing-based attacks.
* **Be cautious with internal-only virtual hosts** to avoid attackers accessing internal domains via Host header manipulation.

***

*   **cache poisoning**

    response have header like `Cache-Control: max-age=30` or `X-Cache: miss`

    Look out if the _Host_ header is **reflected** in the _response markup_ without HTML-encoding, or even used directly in script imports.

    `GET / HTTP/1.1 Host: attackersite.com`The following will be served from the web cache when a victim visits the vulnerable application.`<link src=”<http://attackersite.com/link>" />`

**password reset poisoning attack →** takes the domain name from the _Host_ header when you reset the password

```python
$token = bin2hex(random_bytes(16)); // random token; in this example, 2403e128e40a35bb535e9da01f0faf7b
$resetPasswordURL = "https://{$_SERVER['HTTP_HOST']}/reset.php?token=$token";
```

***

<mark style="color:red;">**Check list**</mark>

Add line wrapping

```python
GET /example HTTP/1.1
    Host: bad-stuff-here
Host: vulnerable-website.com
```

*   add header

    * `X-Host`
    * `X-Forwarded-Server`
    * `X-HTTP-Host-Override`
    * `Forwarded`

    <figure><img src="../../.gitbook/assets/Screenshot 2025-02-22 092755.png" alt=""><figcaption></figcaption></figure>

```
        X-Forwarded-Proto
```

***

[https://github.com/daffainfo/AllAboutBugBounty/blob/master/Host Header Injection.md](https://github.com/daffainfo/AllAboutBugBounty/blob/master/Host%20Header%20Injection.md)
