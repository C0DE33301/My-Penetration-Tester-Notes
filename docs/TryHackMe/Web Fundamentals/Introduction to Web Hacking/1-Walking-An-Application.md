---
layout: default
title: Walking An Application
parent: How The Web Works
nav_order: 1
---

# Walking An Application
Note: Manually review a web application for security issues using only the browsers developer tools.

## Browser Tools
- **View Source**
    - To view the human-readable source code of a website.
- **Inspector**
    - To inspect page elements
- **Debugger**
    - Inspect and control the flow of a page's javascript
- **Network**
    - The network requests a page makes.

## Exploring the website
- Discover features that could potentially be vulnerable.
- Attempt to exploit them to assess.
- Features are usually parts of the website that require some interactivity with the user.
    - A login form
- Note down the individual pages/areas/features with a summary for each one.

|Feature|URL|Summary|
|---|---|---|
|Home Page|/|This page contains a summary of what Acme IT Support does with a company photo of their staff.|
|Latest News|/news|This page contains a list of recently published news articles by the company, and each news article has a link with an id number, i.e. /news/article?id=1|
|News Article|/news/article?id=1|Displays the individual news article. Some articles seem to be blocked and reserved for premium customers only.|
|Contact Page|/contact|This page contains a form for customers to contact the company. It contains name, email and message input fields and a send button.|
|Customers|/customers|This link redirects to /customers/login.|
|Customer Login|/customers/login|This page contains a login form with username and password fields.|
|Customer Signup|/customers/signup|This page contains a user-signup form that consists of a username, email, password and password confirmation input fields.|
|Customer Reset Password|/customers/reset|Password reset form with an email address input field.|
|Customer Dashboard|/customers|This page contains a list of the user's tickets submitted to the IT support company and a "Create Ticket" button.|
|Create Ticket|/customers/ticket/new|This page contains a form with a textbox for entering the IT issue and a file upload option to create an IT support ticket.|
|Customer Account|/customers/account|This page allows the user to edit their username, email and password.|
|Customer Logout|/customers/logout|This link logs the user out of the customer area.|

## Developer Tools
**Developer tool kit**
- **Inspector**
- **Debugger**
- **Network**

### Inspector


### Debugger
- Debugging JavaScript
- **Debugger**
    - Firefox
        - `{ }`, **Pretty Print** option
        - `Breakpoints`, Force the browser to stop processing the javascript and pause the current execution.
            1. Click the line number, turns blue.
    - Safari
- **Sources**
    - Google Chrome

### Network
- **AJAX**, A method for sending and receiving network data in a web application background without interfering by changing the current web page.