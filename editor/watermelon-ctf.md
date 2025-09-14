---
description: this CTF was in BlackHat MEA CTF and CyberX world Intern
cover: ../.gitbook/assets/hqdefault.png
coverY: 0
---

# Watermelon CTF

Watermelon CTF from CyberX\_Internship.

Team Members

-Ahmed Farag

-Khaled Ahmed

-Noura Ayman

-Yousef Fahmy

-Mohamed Abdelaziz

At first lets understand what’s this challenge, No form or any information in the site just this sentence `Welcome to my file sharing API`

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

so let’s check the page source to see if there any endpoint is stored but we not found anything. so let’s run any automation tool discover the endpoint in this website, i prefer to use `dirsearch`

<figure><img src="../.gitbook/assets/image 1 (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



I found 6 endpoint let’s check the first endpoint in the website call `/register` but it’s not acceptable with GET Method

<figure><img src="../.gitbook/assets/image 2 (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



change the GET Method to POST method but nothing but wait i noticed in the response it need header call `Content-Type` and the value is `application/json`

<figure><img src="../.gitbook/assets/image 3 (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

remove anything in the request except `method`,`Host`,`content-type` and send the request again.

<figure><img src="../.gitbook/assets/2024 10 09_16_48_21 Burp_Suite_Professional_v2024.3.1.2_ _Temporary_Project_ _Licensed_to_Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

but what it’s a bad request and the server couldn’t understand the request ..it’s a something strange try to see the website from `Render` in burp but nothing after some overthinking the register function need username or email and password to allow register in any site if we think one minute the content type is `json` then login query in request must be json

<figure><img src="../.gitbook/assets/2024-10-09 16_57_57-Burp Suite Professional v2024.3.1.2 - Temporary Project - Licensed to Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

we success to create account ..

<figure><img src="../.gitbook/assets/giwwphy.gif" alt=""><figcaption></figcaption></figure>

Now let’s start to login in my account has already created it..

add `/login` in the url and intercept it then show the response but the same message in register endpoint `method not allowed`

change it to POST and sent at again but he need the content type is json too so let’s change it and add credential that we send it in the register request

<figure><img src="../.gitbook/assets/2024-10-09 17_10_23-Burp Suite Professional v2024.3.1.2 - Temporary Project - Licensed to Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

we success to login in this website but there is 4 endpoint we don’t know any thing about it

<figure><img src="../.gitbook/assets/image 4 (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

After add the `/profile` to url we get error message say `Unauthorized access` , add cookie in the request and send it again

<figure><img src="../.gitbook/assets/image 5 (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

the response say my id is 3 so is found another 2 account in the server one of this account i used it when we test the application with id 2 that’s meaning the user with id 1 we don’t know anything about it ..

I think a little time we can use this trick to get `IDOR` and start to decrypt the cookie and understand what’s occur..

<mark style="color:green;">→</mark> <mark style="color:green;"></mark><mark style="color:green;">**the cookie contain 3 type**</mark>

1- <mark style="color:purple;">eyJ1c2VyX2lkIjozLCJ1c2VybmFtZSI6ImJ1Z2J1c3RlcnMifQ</mark> is user id, username - base64\
2- <mark style="color:purple;">ZwaOyQ</mark> is the Base64Url-encoded header that indicates the type of token (JWT) and the algorithm used (often HS256 or RS256).

3- <mark style="color:purple;">9CrnMB8eSxHw2p1Ox2AySppQUGA</mark> is the signature, which is used to verify the integrity of the token.

i think a minute this token is fixed and the server just check on the <mark style="color:red;">user id, username</mark> but the token is not fixed and the dream of IDOR is died..

<figure><img src="../.gitbook/assets/batgiphy.gif" alt=""><figcaption></figcaption></figure>

i return to the application map and think a little for `/upload` we can take RCE or LFI from it ..

just return the previous steps → method , cookie but i used chat gpt to create upload request to send it by burp

<figure><img src="../.gitbook/assets/image 6 (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

the file is save in this path `files/2` is a LFI vulnerability ?? but do not rush to judge we need to read this file first by `/files` endpoint

<figure><img src="../.gitbook/assets/2024 10 09_17_51_01 Burp_Suite_Professional_v2024.3.1.2_ _Temporary_Project_ _Licensed_to_Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

Not found !!!!!!!!!!!!!

but wait what’s this path meaning `files/2` ?? there is `file/1` ??

after i revision source code from docker and review the file uploading function i found db.db so let’s make a conflict in the file

so let’s try LFI vulnerability by uploading file..

<figure><img src="../.gitbook/assets/WhatsApp_Image_2024 10 09_at_19.05.10_9531b69c.jpg" alt=""><figcaption></figcaption></figure>

No error occur !! but we have issue we can’t display the content of file..

i try to access file 1 but no response in burp after more thinking i try to use curl command may we get the response

<figure><img src="../.gitbook/assets/WhatsApp_Image_2024 10 09_at_01.03.57_48ff7e7f.jpg" alt=""><figcaption></figcaption></figure>

is data may be a credential for admin ??? username = admin , password = CyberXT34m12

let’s login by this credential

<figure><img src="../.gitbook/assets/2024 10 09_18_14_02 Burp_Suite_Professional_v2024.3.1.2_ _Temporary_Project_ _Licensed_to_Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

when i see this message this is my reaction ..

<figure><img src="../.gitbook/assets/lastgiphy.gif" alt=""><figcaption></figcaption></figure>

there is one endpoint call `/admin` we don’t use it so let’s to know what is do it ??

<figure><img src="../.gitbook/assets/2024 10 09_18_18_51 Burp_Suite_Professional_v2024.3.1.2_ _Temporary_Project_ _Licensed_to_Zer0DayLab.png" alt=""><figcaption></figcaption></figure>

we found the flag hackers !!!

<figure><img src="../.gitbook/assets/gsiphy.gif" alt=""><figcaption></figcaption></figure>

***

***

#### With best wishes Bug Busters Team <a href="#id-11a1d1e7-e13c-800a-b149-cd7dbf2b958a" id="id-11a1d1e7-e13c-800a-b149-cd7dbf2b958a"></a>

<mark style="color:red;">**Notion Link**</mark> : [https://wise-monitor-956.notion.site/New-CTF-Writeup-BY-BUGBUSTERS-TEAM-11a1d1e7e13c8036b0f6c55385e9aa18](https://wise-monitor-956.notion.site/New-CTF-Writeup-BY-BUGBUSTERS-TEAM-11a1d1e7e13c8036b0f6c55385e9aa18)
