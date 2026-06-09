
## Why doe we even need NGINX

Python apps(Flask Django FastAPI) are just Python process 
It can listen on a socket sure but it was never designed to be directly on the internet **WHY**

Python web apps can't efficiently handles: SSL/TLS termination serving static files connection queueing for thousands of concurrent clients. DDoS protections HTTP/2 gzip comperssion or load balancing accross multiple app instances. Nginx handles all of that and the hands of only the application logic request to your Python app

**Request Journey**
![[request_journey.png]]


### Layer 1 Nginx - The front Door
Nginx is an event driven, non-blocking web server written in C. It's single threaded per worker (using `epoll` / `kqueue`) and can handle tens of thousand of simultaneous connections per worker process
#### What Nginx actually does for you

**SSL/TLS termination** - Your Python app has no idea HTTPS even exists. Nginx decrypts the connection and your app only ever see plain HTTP on a local socket. This is huge: Python's SSL handling is slow. Nginx's is native C using OpenSSL / BoringSSL

**Static file serving** - Nginx serces `.css .js .png` directly from disk with `sendfile()` - a kernell syscall that copies bytes from disk to socket without ever entering userspace. Zero Python involved. This is orders of magnitude faster that routing a static file request through Gunicorn -> Flask

**Connection Management**  - Nginx buffers the full incoming request body before forwarding to your app. This is protects you from 'slowloris' attacks (a client sending one byte per second) Without Nginx Gunicorn workers block waiting for slow clients. Nginx queues everything then fires the complete request at your app instantly.

**Reverse Proxy** - Nginx forwards requests to your app over a Unix sockets or local TCP ports. The app sees `127.0.0.1` as the client so you configure `X-Forwarded-For` `X-Real-IP` headers in Nginx which your app then reads.


```nginx

server {
    listen 80;
    server_name example.com;

    # Static files — served by Nginx directly, never touches Python
    location /static/ {
        alias /var/www/myapp/static/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # Everything else → your Python app
    location / {
        proxy_pass http://unix:/run/gunicorn.sock;  # Unix socket, faster than TCP
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Buffer the full request before sending to Gunicorn
        proxy_buffering on;
        proxy_buffer_size 4k;
        proxy_buffers 8 4k;
    }
}
```

## Layer 2: Application  Server   WSGI vs ASGI

**WSGI** (Web Server Gateway Interface) It's synchronous callable-based Python Interface. 
The spec says: your app must be callable that takes `environ start_response` and returns iterable of bytestrings. That's it ONE request at a time per thread/process

```python

def application(environ, start_response):
	status = '200 OK'
	headers = [('Content-Type', 'text/plain')]
	
	start_response(status, headers)
	
	return [b'Hello World']
```

The `environ` dict contains everything: HTTP method path query string headers request body server name port everything from the raw HTTP request translated into a Python dict by the WSGI server

## WSGI

 **WSGI Execution model**
 Gunicorn spawns N worker process
	 Each worker: one Python Interpreter, one copy of your app
		For each request:
			1. worker receives environ dict
			2. Calls app(environ, start_response)
			3. app blocks synchronously (DB queries file IO etc.)
			4. app returns response body
			5. Worker is now free for the next request

The key insight: while a worker is waiting for DB queries it's blocked. Doing Nothing. If all your workers are blocked on slow DB queries simultaneously new requests queue up. This is fundamental limitation of WSGI for I/O heavy workloads

![[wsgi_example.png]]


![[wsgi_nginx.png]]


## ASGI

ASGI (Asynchronous Server Gateway Interface) the modern successor. Instead of synchronous callable your app is in `async` coroutine. It handles connections HTTP requests and WebSockets all through the same interface.

```python
async def application(scope, receive, send):
	# scope: dict with connection info (type, path headers etc)
	# receive; async callable to get messages (request body chunks WS messages)
	# send: async callable to send messages back
		
	assert scope['type'] == 'http'
	
	await send(
	{
		'type': 'http.response.start',
		'status': 200,
		'headers': [[b'content-type', b'text/plain']],
	})
	
	await send({
		'type': 'http.response.body',
		'body': b'Hello World'
	})
```

The ASGI model uses async/await — when your code does await db.query(...), it yields control back to the event loop, which runs other requests while waiting for the DB. One process handles many concurrent requests by interleaving them. This is exactly how Node.js works, brought to Python.

# Layer 3: The Application Servers in Detail
## Gunicorn (WSGI)
Gunicorn (Green Unicorn) is a pre-fork web server. On startup it forks N worker processes from a master. The master watches workers and restarts dead ones. Workers accept connections and call  your WSGI app.

```bash
gunicorn myapp:app \
	--workers 4 # typically 2 * CPU cores + 1
	--worker-class sync \
	--bind unix:/run/gunicorn.sock \
	--timeout 30 \
	--access-logfile - \
	--error-logfile -
```

## Uvicorn (ASGI)

```bash
uvicorn myapp:app \
  --host 0.0.0.0 \
  --port 8000 \
  --workers 4 \           # spawns multiple processes, each with their own event loop
  --loop uvloop \         # much faster than asyncio's default loop
  --http h11              # HTTP/1.1 parser
```

Example of how each module works
![[asgi_vs_wsgi.png]]

### Flask Example


```python

from flask import Flask, requests, jisonify

app = Flask(__name__)


@app.route('/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
	
	user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
    return jsonify(user)
    
```

Imagine that Gunicorn GET HTTP request

```bash
GET /users/23
```
and call Flask
```python
flask_app(environ, start_response)

# here environ is big dictionary
# {
#    "REQUEST_METHOD": "GET",
#    "PATH_INFO": "/users/123",
#    "HTTP_HOST": "example.com",
# }

```

1. **Create object Request**

```python
request = Request(environ)

# request.method
# request.headers
# request.args
# request.json
# request.form
```

2. **Save Request in specail**

But our endpoint don't know about `request`

```python
@app.route("/users/<int:user_id>")
def get_user(user_id):
    print(request.method)
```

That's way Flask put `Request` object in special location like **`thread`**
```python
storage[current_thread_id] = request


# {
#     101: request_for_user1,
#     102: request_for_user2,
# }
```


3.  **request is not a real object**

when we do like this
```python
request.method
```

Actually Flask will do
```python
current_thread = get_current_thread_id()
real_request = storage[current_thread]

return real_request.method
```


```
Browser
   ↓
Gunicorn
   ↓
Flask
   ↓
RequestContext создается
   ↓
Request кладется в thread-local storage
   ↓
View вызывает request.method
   ↓
Proxy находит Request текущего потока
   ↓
Response
   ↓
Контекст удаляется
   ↓
Gunicorn
   ↓
Browser
```



Thread at there structure like this

```
Gunicorn
│
├── Worker #1 (процесс)
│     ├── Thread #1
│     ├── Thread #2
│     └── Thread #3
│
├── Worker #2 (процесс)
│     ├── Thread #1
│     ├── Thread #2
│     └── Thread #3
│
└── Worker #3 (процесс)
      ├── Thread #1
      ├── Thread #2
      └── Thread #3
```

```bash
gunicorn --workers 4 --threads 8 app:app
```
Worker PID 1001
├── Thread 1
├── Thread 2
...
└── Thread 8



# Architecture

```
Internet
   │
   ▼
[Firewall / Cloud Load Balancer]  ← terminates DDoS, handles failover
   │
   ▼
[Nginx]                           ← SSL, static files, gzip, rate limiting
   │
   ├─► /static/  → filesystem     ← zero Python, pure sendfile()
   │
   └─► /api/     → Unix socket
                    │
                    ▼
               [Gunicorn]         ← process manager, N workers
                    │
                    ▼
               [Your App]         ← Flask / Django / FastAPI
                    │
                    ├─► PostgreSQL (via connection pool — pgBouncer)
                    ├─► Redis (cache, sessions, Celery broker)
                    └─► External APIs
```


# WSGI

```
[Gunicorn worker process]
        │
        │  ← WSGI lives HERE — in this arrow
        │
        │  Gunicorn calls:
        │    response = app(environ, start_response)
        │
        │  That function call IS WSGI.
        │  environ = the HTTP request as a Python dict
        │  start_response = callable to set status + headers
        │  response = iterable of bytes (the body)
        │
        ▼
[Your Flask/Django app]
  which is just a Python callable: app(environ, start_response)
```


## asgi

In ASGI applications to save  request context we use `contextvars` 

Classical
```

Thread #1 -> Request A
Thread #2 -> Request B
```

when we do `request.method` flask will search with current thread

**FastAPI(ASGI)**

Imagine that single thread can serve multiple requests
```
Thread #1
	Request A
	Request B
	Request C
```

While our application do some database calls `await database.fetch()` request A can sleep and processor start to serve request B

It can do it with **`contextvars`**

```python
from contextvars import ContextVar

current_user = ContextVar('current_user')

# middleware

current_user.set(user)


# at endpoint

user = currrent_user.get()
```

But FastAPI nowhere can store Request global and we should give request for every request

```python

from fastapi import Request


@app.get('/users')
async def users(request: Request):
	return {
		'path': request.url.path
	}
```



[[Server Architecture]]
[[Django]]
[[Flask]]
[[ASGI]]
[[WSGI]]


