---
layout: default
title: DNS in Detail
parent: How The Web Works
nav_order: 1
---

## Key Terms
- **Domain Name System (DNS)**
    - A simple way to communicate with devices on the internet without remembering the **Internet Protocol Version 4 (IP address)**.
- **Top-Level Domain (TLD)** | admin.tryhackme`.com`
    - **Generic Top Level Domain (gTLD)**
        - Meant to tell the user the domain name's purpose
            - `.com` for commercial purposes
            - `.org` for an organisation
            - `.edu` for education
            - `.gov` for government
    - **Country Code Top Level Domain (ccTLD)**
        - Used for geographical purposes
            - `.ca` for sites based in canada
            - `.co.uk` for sites based in the united kingdom
- **Second-Level Domain** | admin.`tryhackme`.com
- **Subdomain** `admin`.tryhackme.com
    - A subdomain sits on the left-hand side of the second-level domain using a period to separate it.
- **A Record**
    - IPv4 addresses
- **AAAA Record**
    - IPv6 addresses
- **CNAME Record**
    - Resolve to another domain name, `store.tryhackme.com` -> `shops.myshopify.com`
- **MX Record**
    - Directs email to a mail server, `tryhackme.com` -> `aspmx.l.google.com`
    - **Priority Flag**
        - This tells the client in which order to try the servers, this is perfect for if the main server goes down and email needs to be sent to a backup server.
- **TXT Record**
    - Text fields where any text-based data can be stored
        1. List servers that have the authority to send an email on behalf of the domain, this can help fight against spam and spoofed email.
        1. Used to verify ownership of the domain name when signing up for third party services.

## Restrictions
- **Second-level domain** + **Top-Level Domain (TLD)**
    - Limited to 63 characters and can only use a-z 0-9 and hyphens (cannot start or end with hyphens or have consecutive hyphens).
- **Subdomain**
    - Limited to 63 characters and can only use a-z 0-9 and hyphens (cannot start or end with hyphens or have consecutive hyphens).
    - Unlimited subdomain names
- **Subdomain** + **Second-level domain** + **Top-Level Domain (TLD)**
    - The length must be kept to 253 characters or less.

## DNS Request
1. **Client**
    - The resolver checks its **Local Cache**
    - If not, a request to the **Recursive DNS Server** will be made.
1. **Recursive DNS Server**
    - The recursive DNS server checks its **Local Cache**, if a result is found, this is sent back to your computer, and your request ends here.
    - If not, a request to the **Root name server** will be made.
1. **Root DNS server**
    - The root DNS server only redirects to the correct **Top-Level Domain (TLD)** server.

    |||||
    |---|---|---|---|
    |`a.root-servers.net`|`198.41.0.4`|`2001:503:ba3e::2:30`|**Verisign, Inc.**|
    |`b.root-servers.net`|`170.247.170.2`|`2801:1b8:10::b`|**University of Southern California Information Sciences Institute**|
    |`c.root-servers.net`|`192.33.4.12`|`2001:500:2::c`|**Cogent Communications**|
    |`d.root-servers.net`|`199.7.91.13`|`2001:500:2d::d`|**University of Maryland**|
    |`e.root-servers.net`|`192.203.230.10`|`2001:500:a8::e`|**NASA (Ames Research Center)**|
    |`f.root-servers.net`|`192.5.5.241`|`2001:500:2f::f`|**Internet Systems Consortium, Inc**|
    |`g.root-servers.net`|`192.112.36.4`|`2001:500:12::d0d`|**US Department of Defense (NIC)**|
    |`h.root-servers.net`|`198.97.190.53`|`2001:500:1::53`|**US Army (Research Lab)**|
    |`i.root-servers.net`|`192.36.148.17`|`2001:7fe::53`|**Netnod**|
    |`j.root-servers.net`|`192.58.128.30`|`2001:503:c27::2:30`|**Verisign, Inc.**|
    |`k.root-servers.net`|`193.0.14.129`|`2001:7fd::1`|**RIPE NCC**|
    |`l.root-servers.net`|`199.7.83.42`|`2001:500:9f::42`|**ICANN**|
    |`m.root-servers.net`|`202.12.27.33`|`2001:dc3::35`|**WIDE Project**|
1. **Authoritative DNS Server**
    - Responsible for storing the DNS records for a domain name.
    - DNS records all come with a **Time To Live (TTL)** value.
        - The value is a number represented in seconds that the response should be saved for locally until the user have to look it up again
    1. The DNS record is then sent back to the Recursive DNS Server, where a local copy will be cached for future requests.
1. **Back to the client**
    - Then relayed back to the original client that made the request

![A DNS Request](imgs/a-request.png)

## Questions
### What is the CNAME of shop.website.thm?
`nslookup -type=cname store.tryhackme.com`
```diff
Server:         192.168.1.254
Address:        192.168.1.254#53

Non-authoritative answer:
+store.tryhackme.com     canonical name = shops.myshopify.com.

Authoritative answers can be found from:
```
### What is the value of the TXT record of website.thm?
`nslookup -type=txt tryhackme.com`
### What is the numerical priority value for the MX record?
`nslookup -type=mx tryhackme.com`
### What is the IP address for the A record of www.website.thm?
`nslookup -type=a tryhackme.com`