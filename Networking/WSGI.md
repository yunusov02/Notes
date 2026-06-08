# WSGI (Web Server Gateway Interface)

It is a specification that describes how a web server communicates with web applications and how web applications can be chained together to process one request

Web Server(Gunicorn, uWSGI)
Python Applicaiton(Django Flask)

`Web Server < -- WSGI --> Django Applicaiton`


when user opens  `https://myapp.com/products/1`

The request travels like this
```
Browser
   │
   ▼
Nginx
   │
   ▼
Gunicorn (WSGI Server)
   │
   ▼
Django (WSGI Application)
   │
   ▼
URL Router
   │
   ▼
View
   │
   ▼
Response

```

Django or Flask itself not a web server 
This works only for development `python manage.py runserver`
In production we need to listen some port 
`gunicorn core.wsgi:application`

```
                    INTERNET
                        │
                        ▼
                 ┌────────────┐
                 │   NGINX    │
                 └─────┬──────┘
                       │
                       ▼
              ┌─────────────────┐
              │    GUNICORN     │
              │  WSGI SERVER    │
              └───────┬─────────┘
                      │
                      ▼
              ┌─────────────────┐
              │     DJANGO      │
              │  WSGI APP       │
              └───────┬─────────┘
                      │
                      ▼
              ┌─────────────────┐
              │   POSTGRESQL    │
              └─────────────────┘
```




**WSGI**    is not a server, a python module a framework and API or any kind og software

It is simple an interface specification that defines how a server and applicaiton communicate. Both server and application sides are specified in PEP 333

![[wsgi.jpg]]



[[Server Architecture]]