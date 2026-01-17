---
layout: default
title: Race Conditions
parent: Introduction to Web Hacking
nav_order: 9
---

# Race Conditions
A situation in computer programs where the timing of events influences the behaviour and outcome of the program.
- Burp Suite Repeater to exploit race conditions.

## Terms
- **Programs**
- **Process**
- **Thread**
- **Multi-Threading**

## Programs
A set of instructions to achieve a specific task
- Static

**Flask Python "Hello, World!" server, Example**
```py
# Import the Flask class from the flask module
from flask import Flask

# Create an instance of the Flask class representing the application
app = Flask(__name__)

# Define a route for the root URL ('/')
@app.route('/')
def hello_world():
    # This function will be executed when the root URL is accessed
    # It returns a string containing HTML code for a simple web page
    return '<html><head><title>Greeting</title></head><body><h1>Hello, World!</h1></body></html>'

# This checks if the script is being run directly (as the main program)
# and not being imported as a module
if __name__ == '__main__':
    # Run the Flask application
    # The host='0.0.0.0' allows the server to be accessible from any IP address
    # The port=8080 specifies the port number on which the server will listen
    app.run(host='0.0.0.0', port=8080)
```
- **Waiting State**, Listen for incoming connections at port 8080.
- **Ready State**, When it receives an HTTP `GET /` request.
- **Running State**, Sends the HTML page to the client.
- **Waiting State**, Returns to the waiting state.
1. The app is servicing clients sequentially.
1. Client requests are processed one at a time.
```diff
-Flask is multi-threaded by default since version 1.0
```

## Processes
A program in execution
- Dynamic

**Terms**
- **State**
    - A process usually hops between different states
    - **New state**
    - **Ready state**
    - **Running state**
    - **Waiting state**
    - **Terminated state**

`flask run --without-threads --host=0.0.0.0`
- `--without-threads`, To force it to run single-threaded.

## Threads
A lightweight unit of execution
- **Serial**
    - One process is running
    - It serves one user after the other sequentially
    - New users are enqueued
- **Parallel**
    - One process is running
    - It creates a thread to serve every new user
    - New users are only enqueued after the maximum number of running threads is reached

**Gunicorn**
- The "Green Unicorn"
- A python Web Server Gateway Interface (WSGI) HTTP server.
- Spawn multiple worker processes to handle incoming requests simultaneously.

`gunicorn --workers=4 --threads=2 -b 0.0.0.0:8080 app:app`
- `--workers=4`, Four workers ready to tackle clients' requests
- `--threads=2`, Each worker process can spawn two threads.

**Terms**
- **Time-of-Check (TOC)**, 
- **Time-Of-Use (TOU)**

Two threads simulating a task completion by 10% increments
```python
import threading

x = 0  # Shared variable

def increase_by_10():
    global x
    for i in range(1, 11):
        x += 1
        print(f"Thread {threading.current_thread().name}: {i}0% complete, x = {x}")

# Create two threads
thread1 = threading.Thread(target=increase_by_10, name="Thread-1")
thread2 = threading.Thread(target=increase_by_10, name="Thread-2")

# Start the threads
thread1.start()
thread2.start()

# Wait for both threads to finish
thread1.join()
thread2.join()

print("Both threads have finished completely.")
```
- Two threads start together.
- Print a value on the screen.
- There is no guarantee which thread will finish first & 

Output one
```
Thread Thread-1: 40% complete, x = 10
Thread Thread-2: 70% complete, x = 11
Thread Thread-1: 50% complete, x = 12
Thread Thread-2: 80% complete, x = 13
Thread Thread-1: 60% complete, x = 14
Thread Thread-1: 70% complete, x = 16
Thread Thread-2: 90% complete, x = 15
Thread Thread-2: 100% complete, x = 17
Thread Thread-1: 80% complete, x = 18
Thread Thread-1: 90% complete, x = 19
Thread Thread-1: 100% complete, x = 20
Both threads have finished completely.
```
Output two
```
Thread Thread-1: 70% complete, x = 10
Thread Thread-2: 40% complete, x = 11
Thread Thread-1: 80% complete, x = 12
Thread Thread-2: 50% complete, x = 13
Thread Thread-1: 90% complete, x = 14
Thread Thread-2: 60% complete, x = 15
Thread Thread-1: 100% complete, x = 16
Thread Thread-2: 70% complete, x = 17
Thread Thread-2: 80% complete, x = 18
Thread Thread-2: 90% complete, x = 19
Thread Thread-2: 100% complete, x = 20
Both threads have finished completely.
```
- running the program multiple times will lead to different results.

## Exploiting Race Conditions
1. How the target web application receives HTTP requests.
1. How it responds to them.

**Burp Suite Proxy**
1. Click open browser under the **Intercept** tab
1. Click **HTTP history** tab, logs every HTTP request and the respective response.
- Mini Note:
    - A `POST` request
    - Show the target phone number and a transfer amount of $1.5
    - The transaction is successful
- **Race Condition**
    1. Right-click on the POST request,`click Send to Repeater`
    - **Repeater** tab
        1. Click, `+` icon, Click `Create tab group`
        1. Assign a group name, Add tba `1`, Click `Create`
        1. Right-click on `1` tab, Click `Duplicate tab`
        1. `Duplicate 20 times`!


`Send group in sequence (single connection)`
- Establishes a single connection to the server and sends all the requests in the group's tabs before closing the connection.

`Send group in sequence (separate connections)`
- Establishes a TCP connection, sends a request from the group, and closes the TCP connection before repeating the process for the subsequent request.

`Send group in parallel`
- The repeater to send all the requests in the group at once.