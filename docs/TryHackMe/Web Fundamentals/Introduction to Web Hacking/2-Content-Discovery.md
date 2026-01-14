---
layout: default
title: Content Discovery
parent: Introduction to Web Hacking
nav_order: 2
---

# Content Discovery
**Content Discovery method**
- **Manually**
- **Automated**
- **Open-Source Intelligence (OSINT)**

## Manually
- **Robots.txt**
    - Tells search engines which pages they are and aren't allowed to show on their search engine results.
    ```diff
    User-agent: *
    Allow: /
    Disallow: /staff-portal
    ```
- **Favicon**
    - Displayed in the browser's address bar or tab.
    - [OWASP Favicon Database](https://wiki.owasp.org/index.php/OWASP_favicon_database)
    - **Curl**: `curl -s https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum`
    - **PowerShell**
        1. `curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico -UseBasicParsing -o favicon.ico`
        1. `Get-FileHash .\favicon.ico -Algorithm MD5 `
- **Sitemap.xml**
    - A list of every file the website owner wishes to be listed on a search engine.
        - Webpages a bit more difficult to navigate
        - Old webpages
    ```diff
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <url>
            <loc>http://10.80.138.105/</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>1.00</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/news</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/news/article?id=1</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/news/article?id=2</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/news/article?id=3</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/contact</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.80.138.105/customers/login</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
    +        <loc>http://10.80.138.105/s3cr3t-area</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
    </urlset>
    ```
- **HTTP Headers**
    - The webserver software
    - The programming/scripting language in use
    ```diff
    *   Trying 10.80.138.105:80...
    * Established connection to 10.80.138.105 (10.80.138.105 port 80) from 192.168.155.210 port 49790
    * using HTTP/1.x
    > GET / HTTP/1.1
    > Host: 10.80.138.105
    > User-Agent: curl/8.18.0-rc3
    > Accept: */*
    >
    * Request completely sent off
    < HTTP/1.1 200 OK
    +< Server: nginx/1.18.0 (Ubuntu)
    < Date: Wed, 14 Jan 2026 10:14:24 GMT
    < Content-Type: text/html; charset=UTF-8
    < Transfer-Encoding: chunked
    < Connection: keep-alive
    < X-FLAG: THM{HEADER_FLAG}
    <
    <!--
    ```
- **Framework Stack**

## Automated
**Wordlists**
- [**Github - danielmiessler - SecLists**](https://github.com/danielmiessler/SecLists)
- **Automation Tools**
    - `ffuf`
        - `ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u http://10.80.138.105/FUZZ`
    - `dirb`
        - `dirb http://10.80.138.105/ /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt`
    - `gobuster`
        - `gobuster dir --url http://10.80.138.105/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt`

## Open-Source Intelligence (OSINT)
**Google Dorking**
- Google's advanced search engine features

    |||
    |---|---|
    |`site`|`site:tryhackme.com`|
    |`inurl`|`inurl:admin`|
    |`filetype`|`filetype:pdf`|
    |`intitle`|`intitle:admin`|

**[Wappalyzer](https://www.wappalyzer.com/)**
- An online tool and browser extension that helps identify what technologies a website uses, such as frameworks, content Management Systems (CMS), payment processors, etc.

**[Wayback Machine](https://archive.org/web/)**
- A historical archive of websites that dates back to the late 90s.

**GitHub**
- Use GitHub's search feature to look for company names or website names to try and locate repositories belonging to your target.
- Have access to source code, passwords or other content that you hadn't yet found.

**S3 Buckets**
- A storage service provided by Amazon AWS.
- the owner of the files can set access permissions to either make files public, private and even writable.
- `http(s)://{name}.s3.amazonaws.com`
- `http(s)://tryhackme-assets.s3.amazonaws.com`
- **Can be discovered**
    - URLs in the website's page source
    - Github repositories
    - Automating
        - `{name}-assets`
        - `{name}-www`
        - `{name}-public`
        - `{name}-private`