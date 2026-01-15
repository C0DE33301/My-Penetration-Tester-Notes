---
layout: default
title: IDOR
parent: Introduction to Web Hacking
nav_order: 5
---

# Insecure Direct Object Reference (IDOR)
- A type of access control vulnerability.
- When a web server receives user-supplied input to retrieve objects, `files`, `data`, `documents`.
- For example, Logged in profile
    - **User A**, `http://online-service.thm/profile?user_id=1305`
    - **User B**, `http://online-service.thm/profile?user_id=1000`
- For example, User's invoice
    - **User A**, `https://onlinestore.thm/order/1234/invoice`
    - **User B**, `https://onlinestore.thm/order/1000/invoice`

## Encoded IDs
- [**base64 encode**](https://www.base64encode.org)
- [**base64 decode**](https://www.base64decode.org)

## Hashed IDs
[**Database of billions of hash to value**](https://crackstation.net)

For example
- The number `123` -> md5 `202cb962ac59075b964b07152d234b70`

## Unpredictable IDs
1. Create two accounts
1. Swap the id numbers between them
1. View the other users' content using their id number while still being logged in with a different account or not logged in at all.

## Where are they located?
For example
1. `/user/details`, Displaying user information, authenticated through session.
1. **Parameter mining**, Found a parameter called `user_id`.
1. Used to display other users' information, `/user/details?user_id=123`.

## IDOR Example
1. Log in
1. Visit the user's settings
    1. **Investigating how user information gets pre-filled**
        - `Username`, `email address` and `password`
        1. `Developer tools` -> `Network tab`
            - A call to an endpoint with the path `/api/v1/customer?id={user_id}`
                - **Response**
                    ```json
                    {
                    "id": 50,
                    "username": "cat",
                    "email": "cat@email.com"
                    }
                    ```
1. Visit other user infromation
    - Example 1, `https://10-82-140-222.reverse-proxy.cell-prod-eu-west-1c.vm.tryhackme.com/api/v1/customer?id=1`
    - Example 2, `https://10-82-140-222.reverse-proxy.cell-prod-eu-west-1c.vm.tryhackme.com/api/v1/customer?id=3`