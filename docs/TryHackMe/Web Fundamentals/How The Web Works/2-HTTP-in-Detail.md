---
layout: default
title: HTTP in Detail
parent: How The Web Works
nav_order: 2
---

# HTTP in Detail
## Key Terms
- **HyperText Transfer Protocol (HTTP)**
    - HTTP is the set of rules used for communicating with web servers for the transmitting of webpage data, HTML, Images, Videos, etc.
- **HyperText Transfer Protocol Secure (HTTPS)**
    - The secure version of HTTP
    - HTTPS data is encrypted
        - Stops from other users seeing the data you are receiving and sending.
        - Talks to the correct web server and not something impersonating it.
- **Uniform Resource Locator (URL)**
    - An instruction on how to access a resource on the internet.

## **Uniform Resource Locator (URL)**
`http`://`user:password`@`tryhackme.com`:`80`/`view-room` `?id=1` `#task3`
- **Scheme** - `http`
    - What protocol to use for accessing the resource.
- **User** - `user:password`
    - Some services require authentication to log in.
- **Host/Domain** - `tryhackme.com`
    - The domain name or ip address of the server to access.
- **Port** - `80`
    - The port the user is going to connect to.
- **Path** - `view-room`
    - The file name or location of the resource the user is trying to access.
- **Query String** - `?id=1`
    - Extra bits of information that can be sent to the requested path.
    - For example, `/blog?id=1` tells the blog path that the user wish to receive the blog article with the id of 1.
- **Fragment** - `#task3`

## Request
`curl -v https://www.nist.gov/blogs`
```diff
...
+ > GET /blogs HTTP/2

+ > Host: www.nist.gov

+ > User-Agent: curl/8.18.0-rc3
...
```
Line 1
- **Method** - `GET`
- **Path** - `/blogs`
    - `https://www.nist.gov/blogs` -> `GET /blogs HTTP/2`
    - `https://www.nist.gov/blogs/taking-measure/ai-doctors-office-how-standards-can-support-trustworthiness` -> `GET /blogs/taking-measure/ai-doctors-office-how-standards-can-support-trustworthiness HTTP/2`
- **Scheme** - `HTTP/2`
    - Examples
        - `curl -v http://google.com`
            ```diff
            +> GET / HTTP/1.1
            ```
        - `curl -v https://google.com`
            ```diff
            +> GET / HTTP/2
            ```
Line 2
- The website connecting to, ...
Line 3
- The user agent using to connect to the web server, ...

## Response
```diff
...
+ < HTTP/2 200

+ < date: Fri, 09 Jan 2026 07:55:13 GMT

+ < content-type: text/html; charset=UTF-8

+ < server: cloudflare

+ < Content-Length: 219

+ <
```
Line 1
- **Scheme** - `HTTP/2`
    - Examples
        - `curl -v http://google.com`
            ```diff
            +> GET / HTTP/1.1
            ```
        - `curl -v https://google.com`
            ```diff
            +> GET / HTTP/2
            ```
- **Status Code** - `200`

Line 2
- The current date, time and timezone of the web server.

Line 3
- Tells the client what sort of information is going to be sent, such as html, images, videos, pdf, XML.

Line 4
- The web server software and version number.
    - Web server software: `cloudflare`

Line 5
- Tells the client how long the response is, this way we can confirm no data is missing

Line 6
- A blank line to confirm the end of the response

## HTTP Methods
- A way for the client to show their intended action when making an HTTP request.
- **GET Request**
    - This is used for getting information from a web server.
    - Used to view a news article.
- **POST Request**
    - This is used for submitting data to the web server and potentially creating new records.
    - Used to create a new user account.
- **PUT Request**
    - This is used for submitting data to a web server to update information.
    - Used to update a email address.
- **DELETE Request**
    - This is used for deleting information/records from a web server.
    - Used to remove a picture a user has uploaded to their account.

## HTTP Status Codes
A fun way to learn status codes! [View cats here!](https://http.cat/)
|||||
|---|---|---|---|
|100-199|Information Response|
|200-299|Success|
||200|OK|
||201|Created|<ul><li>Created a new user or blog post article</li></ul>|
|300-399|Redirection|
||301|Moved Permanently|
||302|Found|
|400-499|Client Errors|
||400|Bad Request|
||401|Not Authorised|<ul><li>Try to edit a profile without logging in first</li></ul>|
||403|Forbidden|
||404|Page Not Found|<ul><li>Tried to access a page that doesn't exist</li></ul>|
||405|Method Not Allowed|
|500-599|Server Errors|
||500|Internal Service Error|
||503|Service Unavailable|<ul><li>If the web server cannot access its database and the application crashes</li></ul>|

## Request Headers
- **Host** | `> Host: google.com`
    - The visted website
- **User-Agent** | `> User-Agent: curl/8.18.0-rc3`
    - The tool used to visit the website
- **Content-Length** | `< Content-Length: 219`
    - Tells the web server how much data to expect in the web request.
- **Accept-Encoding**
    - Tells the web server what types of compression methods the browser supports so the data can be made smaller for transmitting over the internet.
- **Cookie**
    - Data sent to the server to help remember the user's information

## Response Headers
- **Set-Cookie**
    - Information to store which gets sent back to the web server on each request
- **Cache-Control**
    - How long to store the content of the response in the browser's cache before it requests it again.
- **Content-Type**
    - This tells the client what type of data is being returned.
    - Helps the browser know how to process the data.
- **Content-Encoding**
    - What method has been used to compress the data to make it smaller when sending it over to the internet.

## Cookies
A small piece of data that is stored on the user's computer.
- Cookies can be used to remind the web server who you are
- Some personal settings for the website
- If the user has visited the website before
1. Cookies are saved when a user receives a `Set-Cookie` header from a web server.
1. Then every further request the user makes, the user sends the cookie data back to the web server.

![Cookies!](imgs/cookies.png)

## How to view the cookies
1. Click the `F12` key
1. Click the `Storage` tag
1. Click the `Cookies` tag

## Questions!
### Make a GET request to /room page

### Make a GET request to /blog page and set the id parameter to 1
Note: Use the gear button on the right to manage URI parameters

### Make a DELETE request to /user/1 page

### Make a PUT request to /user/2 page with the username parameter set to admin
Note: Use the gear button on the right to manage body parameters

### Make a POST request to /login page with the username of thm and a password of letmein
Note: Use the gear button on the right to manage body parameters