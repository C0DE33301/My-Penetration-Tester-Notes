---
layout: default
title: How Websites Work
parent: How The Web Works
nav_order: 3
---

# How Websites Work

## How websites work
1. The browser makes a request to a web server asking for information about the website.
1. The website responds with data that the browser uses to show the page.

- **Front End (Client-Side)**
    - How the browser renders a website
- **Back End (Server-Side)**
    - A server that processes the request and returns a response.

## Sensitive Data Exposure
- Occurs when a website doesn't properly protect (or remove) sensitive clear-text information to the end-user; usually found in a site's frontend source code.
- A website developer may have forgotten to remove login credentials, hidden links to private parts of the website or other sensitive data shown in HTML or Javascript
    - There could be html comments with temporary login credentials.

## HTML Injection
- Is a vulnerability that occurs when unfiltered user input is displayed on the page.
- Client-side