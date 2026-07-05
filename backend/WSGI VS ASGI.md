
## How a web request reaches your Python code

**What actually happens when you open a website**
```bash
https://example.com/products/15
```

The browser sends an HTTP request
```bash
GET /products/15 HTTP/1.1
Host: example.com
```

That request travels across the internet until it reaches the server
On the server something must be listening on a TCP port usually (80 or 443)

```bash
Browser -> Internet -> Server
```

*Your Python code is not listening on that port by itself*

A server process is

```bash
Browser -> Server process -> Python Application
             
             Uvicorn
             Gunicorn
```

Without one of these servers your Python application cannot receive HTTP requests

#### Why it is not possible

when we execute python code
```python
print("Hello World")
```


Python executes the code once and exits

But web applications must work
* start
* keep running
* listen for connections
* accept thousands of requests
* send responses
* continue waiting

#### What does the server actually do?

Imagine 5 users visit your website

```bash
User 1 ─┐
User 2 ─┤
User 3 ─┼────► Server
User 4 ─┤
User 5 ─┘
```

The Server
* Accepts the TCP connection
* Reads the HTTP request
* Calls your Python application
* Gets the response
* Sends it back
* waits for the next request

Your application only focuses on business logic

```python
def get_product():
	return {
		"id": 15,
		"name": "laptop"
	}
```
The server handles all networking details your app only handles business logic

But many frameworks like Flask Django and FastAPI used to have different ways of communicating with servers thats why we needed WSGI standart rules every frameworks should follow to these rules
## WSGI

**WSGI** stands for **Web Server Gateway Interface**

Notice the last word: *Interface*
it is not 
* a web server
* a framework
* a Python library
It is a specification (contract) that defines how a web server communicates with a Python web application

Think of it like a USB standard. 

### Before WSGI
Imagine two frameworks

```bash
Framework A  <---> Server A
Framework B  <---> Server B
```

Each server understands only its own framework
If you change frameworks you might also need to change servers
### After WSGI
Now everyone agrees on one interface:
```bash
             WSGI Standard
                   │
 ┌─────────────────┼─────────────────┐
 │                 │                 │
Django          Flask           Pyramid
 │                 │                 │
 └─────────────────┼─────────────────┘
                   │
         Gunicorn / uWSGI / Waitress
```

As long as both the application and the server follow the WSGI specification they can work together

### The WSGI request flow

Suppose 
```bash
GET /users/15
```
the flow will look like this
```bash
Browser
    │
HTTP Request
    │
    ▼
Gunicorn (WSGI Server)
    │
Creates WSGI environment
    │
Calls your application
    │
Application returns response
    │
Gunicorn sends HTTP response
    ▼
Browser
```

### A WSGI application is just a function

At its core, a WSGI application looks like this

```python

def application(environ, start_response):
	start_response(
		"200 OK",
		[("Content-Type", "text/plain")]
	)
	
	return [b"Hello World"]
```

* `environ` is dictionary containing information about the HTTP request for example
```bash
{
    "REQUEST_METHOD": "GET",
    "PATH_INFO": "/users/15",
    "QUERY_STRING": "",
    "SERVER_NAME": "localhost",
    "SERVER_PORT": "8000",
}
```

Instead of parsing raw HTTP your application receives a convenient Python dictionary
* `start_response` This is a function provided by the server
	Your application calls it to tell the server:
		* The HTTP status
		* the response headers
```bash
start_response(
    "200 OK",
    [("Content-Type", "application/json")]
)
```

### In Django
```python
from django.core.wsgi import get_wsgi_application

application = get_wsgi_application()
```


### WSGI Server VS WSGI Application

#### WSGI server
* listens for incoming HTTP requests
* parses the HTTP request
* calls your WSGI application
* receives the application's response*
* sends the HTTP response back to the client 
* examples (Gunicorn uWSGI)
#### WSGI Application
A WSGI application is your Python web application that follows the WSGI specification

```python

def application(environ, start_response):
    start_response(
        "200 OK",
        [("Content-Type", "text/plain")]
    )
    return [b"Hello"]
    
# or in django 

# wsgi.py
from django.core.wsgi import get_wsgi_application

application = get_wsgi_application()

```

## Gunicorn

### Gunicorn (Green Unicorn) is a WSGI Server

it is not
* a web framework
* a web application
* an ORM

*Its job is to run your WSGI Application*

Gurnicorn the the process that runs it

```bash
Gunicorn (WSGI Server)
        │
        ▼
Django (WSGI Application)
```


Imagine you run:
```bash
gunicorn myproject.wsgi:application
```
**Gunicorn performs these steps**
- Starts a server process
- Opens a TCP port (usually 8000)
- waits for incoming HTTP requests
- imports your WSGI application
- Calls the application for every request
- Sends the response back to the client

### Gunicorn Workers

Instead of one Python process Gunicorn can create multiple worker processes

```bash
gunicorn --workers 4 myproject.wsi:application
```
Architecture
```bash
               Gunicorn Master
                      │
      ┌───────────────┼───────────────┐
      ▼               ▼               ▼
  Worker 1       Worker 2       Worker 3
      │               │               │
   Django         Django         Django
```

Each worker is a seperate Python process with its own memory and interpretater
without workers each new requests wait until other older requests are done

**Master Process** does not handle user requests. its responsible for starting workers monitoring workers restarting crashed workers gracefully reloading the application managing the worker lifecycle

#### Gunicorn documentation says
```
workers = (2 x number_of_CPU_cores) + 1
```

## ASGI

Why WSGI is not enough

Imagine 
```bash
Request 1
    │
    ▼
Worker 1
    │
    ▼
Database (wait 2 seconds)
```

During those 2 seconds worker 1 is blocked it cannot process another request.

#### Another Problem
Imagine you are building a chat application
when Alice sends a message Bob should receive it immediately

That requires a connection that stays open

```bash
Browser  <===================>  Server
           Open for minutes
```


### Enter ASGI
**ASGI** stands for **Asynchronous Server Gateway Interface**

like WSGI but asynchronously

```bash
ASGI

Browser
    │
Uvicorn
    │
FastAPI / Django ASGI
```

```bash
Application
   │
Start database query
   │
While waiting...
   │
Process another request
```

```bash
uvicorn myproject.asgi:application
```


## Uvicorn

Exactly same as Gunicorn but

```bash
GET /users/1
```

```bash

Browser
    │
HTTP Request
    │
    ▼
Uvicorn
    │
Creates ASGI scope
    │
Calls ASGI application
    │
Gets response
    │
Sends HTTP response
    ▼
Browser
```


uvicorn calls

```python
await application(scope, receive, send)
```

where
`scope` = request information (similar to environ)
`receive` = receive incoming events/messages
`send` = send outgoing events/messages

**scope**
```python
scope = {
    "type": "http",
    "method": "GET",
    "path": "/users/1",
}
```



```bash

# Gunicorn managing Uvicorn workers
gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app
```
```bash

                  Gunicorn Master
                         │
      ┌──────────────────┼──────────────────┐
      ▼                  ▼                  ▼
  Uvicorn Worker 1   Uvicorn Worker 2   Uvicorn Worker 3
         │                  │                  │
         └──────────────► ASGI App ◄───────────┘
```






## Production architecture (Nginx -> Gunicorn/Uvicorn -> Django/FastAPI)


### The Complete Architecture

```bash
                Internet
                    │
                    ▼
              Nginx (Reverse Proxy)
                    │
                    ▼
     Gunicorn/Uvicorn (Application Server)
                    │
                    ▼
        Django / FastAPI (Application)
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
     PostgreSQL            Redis
```


![[wsgi_vs_asgi_request_flow.png]]![[request_journey.png]]


[[Django]]
[[djangorestframework]]
[[Flask]]
