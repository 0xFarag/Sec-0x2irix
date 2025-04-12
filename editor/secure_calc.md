---
description: >-
  this ctf was in ASCWG  Qualifications 2024 (Web Exploitation) and CyberX
  intern
---

# Secure\_Calc

Secure\_Calc CTF from CyberX\_Internship.

Team Members

-Khaled Ahmed

-Noura Ayman

-Ahmed Farag

-Yousef Fahmy

At first, there are two files you need to download to be able to run the CTF.

I initiated the pentest by fuzzing for potential hidden endpoints using a wordlist to explore the application's directory structure. This was aimed at discovering additional entry points that might not be visible through the regular interface.

![image.webp](../.gitbook/assets/image.webp)

After launching my server at `http://localhost:3008/`, I used Burp Suite to capture requests, .experimented by changing the request method and content type, attempting to send various JSON payloads.

Despite my efforts—modifying the method and trying different headers—the responses were consistently **unhelpful :(** . Each attempt to elicit a different response from the server yielded no useful results, but it sharpened my understanding of the application's behavior.

![image (1).webp](../.gitbook/assets/image_\(1\).webp)

realized I needed a deeper dive into how the server was structured. by see source code and get :

**Endpoint Discovery**: By setting up my own `/calc` endpoint and

`{ eqn }` opject , I could test how different endpoints might handle user input, such as equations or other objects

try to catch this end point `http://localhost:3008/calc`

![image (2).webp](../.gitbook/assets/image_\(2\).webp)

now try to modify it

1:change to POST and get

![image (3).webp](../.gitbook/assets/image_\(3\).webp)

When I received a response, I noticed that the `Content-Type` was set to `application/json`. This indicated that the server expected a JSON payload. Based on this, I modified my requests to include a JSON body in the format the server might accept.

and get response :)

![image (4).webp](../.gitbook/assets/image_\(4\).webp)

now try to modify data and see response

![image (5).webp](../.gitbook/assets/image_\(5\).webp)

validation!!!!!

**step back** to index.js code

The code incorporates key validation checks to enhance security:

1. **Missing Equation**: If the `eqn` field is absent in the request, the server responds with a `400 Bad Request` status and an appropriate error message.
2. **Invalid Format**: The server checks for the presence of alphabetic characters in the equation. If any are found, it returns a `400 Bad Request` status with an error indicating an invalid format.

![code2.png](../.gitbook/assets/code2.png)

After that, I started using **Postman** and tried several requests from it. doing some math calculations, and I was getting values like in the screenshot.

![Screenshot 2024-09-30 020017.png](<../.gitbook/assets/Screenshot_2024 09 30_020017.png>)

I tried making some incorrect calculations, but still, nothing changed.

However, I noticed there’s an issue with the power calculations.

For example, 4^2 is supposed to give 16

![Screenshot 2024-09-30 020138.png](<../.gitbook/assets/Screenshot_2024 09 30_020138.png>)

but it’s showing 6, which is strange. But I thought maybe it’s a problem with the code itself.

So, I went back to analyze the files I had downloaded to understand how the code works, and I started reviewing the

**index.js** file. I found that there’s input protection written like this:

```
[a-zA-Z]
```

This regex means it’s preventing any alphabetic characters from being sent.

I left the **index.js** file and started working on the **package.js** file.

As for the second file,

, I found a few things that surprised me.

It's a page that only contains specific versions that the CTF relies on.

![code.png](../.gitbook/assets/code.png)

I thought I'd try to see if there's a CVE, for example.

I navigated to the file paths, opened the CMD, and typed

```
npm audit
```

to see if there were any vulnerabilities or issues.

![Screenshot 2024-09-30 004447.png](<../.gitbook/assets/Screenshot_2024 09 30_004447.png>)

Indeed, I found that the version of VM2

had a vulnerability, so I started investigating the matter.

I searched on Google and found that this CVE is indeed well-known and very dangerous.

It allows me to send code in the request and get a `reverse shell.`

I found a specific Proof of Concept (PoC) and decided to try it out.

![code1.png](../.gitbook/assets/code1.png)

However, there was a problem: the request only accepted specific input because a regex was set in the

index.js file.

I couldn’t use the PoC because of that.

As we mentioned, the regex was

```
[a-zA-Z]
```

, so the code couldn’t contain any letters or words to bypass the regex.

I tried encoding in multiple ways, but all of them failed because the output contained letters.

But when I tried using Unicode, the encoding looked like this:

```
\u1234
```

![Screenshot 2024-09-30 005145.png](<../.gitbook/assets/Screenshot_2024 09 30_005145.png>)

I tried changing the letter "u" to any other letter, and it passed the regex!!!.

![Screenshot 2024-09-30 005219.png](<../.gitbook/assets/Screenshot_2024 09 30_005219.png>)

I tested all the letters and found that only these passed:

```
r-t-f-b-n
```

. The others didn’t.

Honestly, I didn’t know what to do with those letters, and I couldn't think of any ideas at that moment.

So I thought, why not try encoding using parentheses? I had seen this method before in a write-up, and that's the benefit of reading many write-ups.

I searched again and found that there’s a tool called **JSFuck** that can encode the code in the way you see.

![Screenshot 2024-09-30 005435.png](<../.gitbook/assets/Screenshot_2024 09 30_005435.png>)

I was very excited and decided to try it.

I tried a normal request to see if it would give me a response or not.

I encoded `7*7`, copied the code, and pasted it into Postman, then sent the request.

![Screenshot 2024-09-30 005545.png](<../.gitbook/assets/Screenshot_2024 09 30_005545.png>)

The surprise here is that it actually sent me a response of `49`, and that’s when I realized that I had probably made it halfway.

after i found the encode tool worked well so let’s try to encode the POC code and send the request again

![2024-09-30\_00\_21\_58-Clipboard.webp](<../.gitbook/assets/2024 09 30_00_21_58 Clipboard.webp>)

The response is changed but what’s the meaning of `[object,object]` after more search i understand the response for this equation is **blind**

![giphy\_(1).gif](../.gitbook/assets/giphy_\(1\).gif)

I used Beeceptor or Burp collaborator to see the response and the command that’s execute on the server is `id`

![code3.png](../.gitbook/assets/code3.png)

This code contains several tricks that leverage concepts like `Promises`, `recursive function errors`, and manipulation of the `Promise specification`. The primary goal appears to be executing a malicious command by calling some Node.js methods.

### The Goal of the Code:

The primary purpose is to send information about the user's identity (output of the `id` command) to an external server using **curl**. This type of code is considered **malicious** because it tries to exploit Node.js capabilities to execute system commands and send data to an external server, which is a harmful behavior.

After send this request again by postman and open the beecepter i found this request which show the `uid,gid,etc`

![Screenshot\_2024-09-29\_141747.webp](<../.gitbook/assets/Screenshot_2024 09 29_141747.webp>)

I succeed to gain run code the on server….

![giphy\_(2).gif](../.gitbook/assets/giphy_\(2\).gif)
