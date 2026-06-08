
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

**Conection Management**  - Nginx buffers the full incoming request body before forwarding to your app. This is protects you from 'slowloris' attacks (a client sending one byte per second) Without Nginx Gunicorn workers block waiting for slow clients. Nginx queues everything then fires the complete request at your app instantly.

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

 






[[Server Architecture]]
[[Django]]




