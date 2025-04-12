# Cross-Site Request Forgery \[CSRF]

Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.

For a CSRF attack to be possible, three key conditions must be in place:

* **A relevant action.** There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).
* **Cookie-based session handling.** Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.
* **No unpredictable request parameters.** The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.

Although CSRF is normally described in relation to cookie-based session handling, it also arises in other contexts where the application automatically adds some user credentials to requests, such as HTTP Basic authentication and certificate-based authentication.

* Basic Payload to automatically submit the request when the web page opens.

```html

<html>
  <body>
    <form action="<https://vulnerable-website.com/email/change>" method="POST">
      <input type="hidden" name="email" value="pwned@evil-user.net" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

CSRF will be `login`, `logout`, `resetpass`, `change password,` `add-cart`, `like`, `comment`, `profie change`, `user details change`, `blance transffer`, `subscription`, etc

***

#### bypass

```
-Change Request Method [POST => GET]

-Remove Total Token Parameter

-Remove The Token, And Give a Blank Parameter

-Copy a Unused Valid Token , By Dropping The Request and Use That Token

-Many Web applications use a Static and Dynamic part CSRF token 

-Use Own CSRF Token To Feed it to Victim

-Replace Value With Of A Token of Same Length

-Reverse Engineer The Token

-Extract Token via HTML injection

-Switch From Non-Form `Content-Type: application/json` or `Content-Type: application/x-url-encoded` To `Content-Type: form-multipart`

-Change/delete the last or frist character from the token

-Change referrer to Referrer

-Bypass the regex
  If the site is looking for “bank.com” in the referer URL, maybe “bank.com.attacker.com” or “attacker.com/bank.com” will work.

-Remove the referer header (add this <meta name=”referrer” content=”no-referrer”> in your payload or html code)

-Clickjacking
- Remove Referrer header and use meta tag in head of page 
	<head>
		<meta name="referrer" content="no-referrer">
	</head>
- May use "HttpOnly; SameSite=Strict" in response or "secure" with cookie where that prevent JS access the cookie
```

***

#### **Mitigation**

* The `SameSite` attribute can be used to control whether and how cookies are submitted in cross-site requests. By setting the attribute on session cookies, an application can prevent the default browser behavior of automatically adding cookies to requests regardless of where they originate.
* The `SameSite` attribute is added to the `Set-Cookie` response header when the server issues a cookie, and the attribute can be given two values, `Strict` or `Lax`. For example:

`SetCookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Strict;`

`SetCookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Lax;`

* If the `SameSite` attribute is set to `Strict`, then the browser will not include the cookie in any requests that originate from another site. This is the most defensive option, but it can impair the user experience, because if a logged-in user follows a third-party link to a site, then they will appear not to be logged in, and will need to log in again before interacting with the site in the normal way.
* If the `SameSite` attribute is set to `Lax`, then the browser will include the cookie in requests that originate from another site but only if two conditions are met:
  * The request uses the GET method. Requests with other methods, such as POST, will not include the cookie.
  * The request resulted from a top-level navigation by the user, such as clicking a link. Other requests, such as those initiated by scripts, will not include the cookie.
* Using `SameSite` cookies in `Lax` mode does then provide a partial defense against CSRF attacks, because user actions that are targets for CSRF attacks are often implemented using the POST method. Two important caveats here are:
  * Some applications do implement sensitive actions using GET requests.
  * Many applications and frameworks are tolerant of different HTTP methods. In this situation, even if the application itself employs the POST method by design, it will in fact accept requests that are switched to use the GET method.
