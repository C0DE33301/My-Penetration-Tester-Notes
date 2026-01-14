---
layout: default
title: Subdomain Enumeration
parent: Introduction to Web Hacking
nav_order: 3
---

# Subdomain Enumeration
**Subdomain enumeration methods**
- **Brute Force**
- **Open-Source Intelligence (OSINT)**
- **Virtual Host**

## Open-Source Intelligence (OSINT)
**Secure Sockets Layer/Transport Layer Security (SSL/TLS) Certificates**
- Created for a domain by a **Certificate Authority (CA)**.
    - CA takes part in **Certificate Transparency (CT)** logs
- [CRT](https://crt.sh/), A searchable database of certificates that shows current and historical results.

**Search Engines**
- `site:*.domain.com -site:www.domain.com`
    - Would only contain results leading to the domain name domain.com but exclude any links to www.domain.com.
    - Only shows domain names belonging to domain.com.
- `site:*.tryhackme.com -site:www.tryhackme.com`

**Sublist3r**
- `./sublist3r.py -d acmeitsupport.thm`

## Brute Force
- **Domain Name System (DNS)**
    - `dnsrecon -t brt -d 10.80.158.240`

## Virtual Host
**Maps domain names to IP addresses**
- `/etc/hosts`
- `C:\windows\system32\drivers\etc\hosts`
- `ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.80.158.240`
    - `-w`, The wordlist
    - `-H`, Adds/Edits a header
        - `-H "Host: FUZZ.acmeitsupport.thm"`
            - The `FUZZ`, Trys all the options from the wordlist.
- `ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.80.158.240 -fs 395`
    - `-fs`, The page size result
    - For example, `zytronic   [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 112ms]`