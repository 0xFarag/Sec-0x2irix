# information disclosure

## What's the information disclosure?

> information leakage, when website unintentionally for sensitive data

## **Example for sensitive data**

* Data about user such as username or financial information
* Sensitive commercial or business data
* Technical details or website infrastructure

## **Examples of information disclosure**

Revealing the names of hidden directories (`robots.txt` , `/sitemap.xml`)

* Access to source code via temporary backup
* Mentioning database table or column name in error message
* exposing highly sensitive information, such as credit card details
* Hard-coding API keys, IP addresses, database credentials, and so on in the source code
* an application might unintentionally reveal sensitive information about its internal workings, such as the existence or absence of certain resources, usernames, or other data, by subtly altering its behavior based on different inputs or conditions.
* server version

***

## **How to test for information disclosure vulnerabilities**

1. Fuzzing
2. Burp Scanner
3. Burp engagement tools
4. engineering informative response

**Fuzzing :** identify interesting parameters and try to submitting unexpected data

***

#### **Version Leak**

```
step1. go to the target says- <https://redacted.com>
step2. open view page source
step3. check for path, directories
step4. go that path says- <https://redacted.com/theme/css/file.css>
step5. try to visit all directories and check it is accessible or not.
step5. if it is give 403
step6. add %0, %m, %2e, says- <https://redacted.com/%0theme> and then check the response, it will show the running server name, and version information.
```
