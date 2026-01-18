---
layout: default
title: Command Injection
parent: Introduction to Web Hacking
nav_order: 10
---

# Command Injection

## Key Terms
- **Command Injection**
    - The abuse of an application's behaviour to execute commands on the operating system.
- **Remote Code Execution (RCE)**
    - The ability to remotely execute code within an application.

## Discovering Command Injection
- Applications use functions in programming languages, `PHP`, `Python`. and `NodeJS` to pass data to and to make system calls on the machine's operating system.
    - For example, The application takes data that a user enters in an input field named $title to search a directory for a song title
    ```php
    <?php

    $songs = "/var/www/html/songs"

    if (isset $_GET["title"]) {
        $title = $_GET["title"];

        $command = "grep $title /var/www/html/songtitle.txt";

        $search = exec($command);
        
        if ($search == "") {
            $return = "<p>The requested song</p><p>$title does</p><b>not</b><p>exist!</p>";
        } else {
            $return = "<p>The requested song</p><p>$title does</p><b>exist!</b>";
        }

        echo $return;

    }

    ?>
    ```
    1. The application stores MP3 files in a directory contained on the operating system.
    1. The user inputs the song title they wish to search for. The application stores this input into the `$title` variable.
    1. The data within this `$title` variable is passed to the command `grep` to search a text file named *songtitle.txt* for the entry of whatever the user wishes to search for.
    1. The output of this search of songtitle.txt will determine whether the application informs the user that the song exists or not.
- Example
```python
import subprocess

from flask import Flask
app = Flask(_name_)

def execute_command(shell):
    return subprocess.Popen(shell, shell=true, stdout=subprocess.PIPE)stdout.read()

@app.route(/<shell>)
def command_server(shell):
    return execute_command(shell)
```
1. The "flask" package is used to set up a web server
1. A function that uses the "subprocess" package to execute a command on the device
1. We use a route in the webserver that will execute whatever is provided
    - For example, to execute `whoami`, we'd need to visit `http://flaskapp.thm/whoami`.

## Exploiting Command Injection
**Blind command injection**
- There is no direct output from the application when testing payloads.
    - **Note**: You will have to investigate the behaviours of the application to determine whether or not your payload was successful.

**Verbose command injection**
- There is direct feedback from the application once you have tested a payload.
    - **For example**: Running the whoami command to see what user the application is running under, the web application will output the username on the page directly.

## Blind command injection
- `ping`
- `sleep`
- `curl`
    - `curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`
- Forcing some output by using redirection operators, `>`.
- Execute commands such as `whoami` and redirect that to a file.

## Verbose command injection
**Linux**
- `whoami`
- `ls`
- `ping`
- `nc`

**Windows**
- `whoami`
- `dir`
- `ping`
- `timeout`

**PHP, Functions interact with the operating system to execute commands via shell**
- `Exec`
- `Passthru`
- `System`

```php
<input type="text" id="ping pattern="[0-9]+""></input>

<?php

echo passthru("/bin/ping -c 4 "$_GET["ping"]."");

?>
```
1. Only accept a specific pattern of characters, the digits 0-9.
1. Execute this data which is all numerical.

## Input sanitisation
- sanitising any input from a user that an application uses is a great way to prevent command injection.

For example, An input field that only accepts numerical data or removes any special characters such as `>`, `&` and `/`.


```php
<?php

if (!filter_input(INPUT_GET, "number", FILTER_VALIDATE_NUMBER)) {

}

?>
```
1. The `filter_input` PHP function is used to check whether or not any data submitted via an input form is a number or not.
1. If it is not a number, it must be invalid input.

## Bypassing Filters
An application may strip out quotation marks
```php
$payload = "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"
```

## Practical
**User**
- `http://10.81.156.140/index.php?address=127.0.0.1;whoami`

**Flag**
- `http://10.81.156.140/index.php?address=127.0.0.1;cat%20/home/tryhackme/flag.txt`