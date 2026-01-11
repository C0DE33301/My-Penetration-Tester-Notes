---
layout: default
title: Putting it all together
parent: How The Web Works
nav_order: 4
---

# Putting it all together

## Key Terms
- **Load Balancers**
    - Ensuring high traffic websites can handle the load.
    - Providing a failover if a server becomes unresponsive.
- **Algorithms**
    - The load balancer uses different algorithms to help it decide which server is best to deal with the request
    - **Round-Robin**
        - sends it to each server in turn, Request 1 goes to server 1, request 2 goes to server 2, and so on.
    - **Weighted**
        - Checks how many requests a server is currently dealing with and sends it to the least busy server.
- **Health Check**
    - Perform periodic checks with each server to ensure they are running correctly.
    - If a server doesn't respond appropriately or doesn't respond, the load balancer will stop sending traffic until it responds appropriately again.
- **Content Delivery Networks (CDN)**
    - An excellent resource for cutting down traffic to a busy website.
    - Geographically distributed group of servers that caches content close to end users.
- **Databases**
- **Web Application Firewall (WAF)**
    - It's primary purpose is to protect the webserver from hacking or denial of service attacks.
    - It analyses the web requests for common attack techniques.
- **Web Server**
    - A software that listens for incoming connections and then utilises the HTTP protocol to deliver web content to its clients
        - **Apache**
        - **Nginx**
        - **IIS**
        - **NodeJS**
- **Virtual Hosts**
    - Web servers can host multiple websites with different domain names.
- **Static Content**
    - Content that never changes.
- **Dynamic Content**
    - Content that could change with different requests.

## Web Server
- **Apache**
    - Default location, `/var/www/html`
- **Nginx**
    - Default location, `/var/www/html`
- **IIS**
    - Default location, `C:\inetpub\wwwroot`
- **NodeJS**