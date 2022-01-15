---
layout: post
title: Deploying FastApi with Gunicorn
description: 
comments: False
hide: false
search_exclude: true
toc: true
categories: [Python, FastAPI, Gunicorn, Locust, Bangla]
---
FastApi is getting more popular as the days are passing.
There are several ways that we can follow to deploy our fastapi application in production. In this blog post we are going to explore some of the methods that might be appropriate for your project.


### CPU-Bound and I/0-Bound

What does this mean?


### What is Uvicorn?


### What is Gunicorn?


### What is worker?



### Different Type of Workers





## Resources:

### Gunicorn, Uvicorn

- [Gunicorn gevent workers vs Uvicorn ASGI](https://stackoverflow.com/questions/62543342/gunicorn-gevent-workers-vs-uvicorn-asgi)

- [Deploying Gunicorn](https://docs.gunicorn.org/en/stable/deploy.html)

- [Uvicron](https://www.uvicorn.org/)

- [Better performance by optimizing Gunicorn config](https://medium.com/building-the-system/gunicorn-3-means-of-concurrency-efbb547674b7)

- [Gunicorn Design](https://docs.gunicorn.org/en/0.16.1/design.html)

- [Gunicorn Worker Types: How to choose the right one](https://dev.to/lsena/gunicorn-worker-types-how-to-choose-the-right-one-4n2c)

- [Gunicorn Workers and Threads](https://stackoverflow.com/questions/38425620/gunicorn-workers-and-threads)

- [A brief description of the architecture of Gunicorn.](https://docs.gunicorn.org/en/latest/design.html#sync-workers)


### WSGI/ASGI

- [WSGI for Web Developers (Ryan Wilson-Perkin)](https://youtu.be/WqrCnVAkLIo)

- [An introduction to ASGI, Asynchronous Server Gateway Interface - Philip Jones - PyLondinium19](https://youtu.be/t3gCK9QqXWU)

- [An ASGI Server from scratch](https://av.tib.eu/media/50084)

### Locust

- [An Intro to Load Testing with Locust and Python - Gabriel Boorse](https://youtu.be/uvs4cq6JCeU)

- [Locust: Load test your REST API](https://shekhargulati.com/2018/12/06/locust-load-testing-your-rest-api/)


### Others

- [What Is the Python Global Interpreter Lock (GIL)?](https://realpython.com/python-gil/)

- [Threading and multiprocessing](https://uwpce-pythoncert.github.io/SystemDevelopment/threading-multiprocessing.html#threads-versus-processes-in-python)