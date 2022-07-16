---
layout: post
title: Multi-stage Docker Build for Python Application
description: Multi-stage docker build for python application
comments: false
toc: True
categories: [Python, Poetry, Docker, English]
image: images/multi-stage-build-image-dark.png
---
Recently, I have started learning about Docker multi-stage build which can help to reduce docker image size. I have seen its use in multiple GitHub projects and blog posts. So, I tried to understand it and create a version of my own to use later in my projects.

Another thing, I have started using [poetry](https://python-poetry.org/), a python packaging and dependency management tool for my python projects. Before using poetry, I was maintaining `requirements.txt` files for dependency management.

In this blog post, I will be presenting an approach for reducing image size with Docker multi-stage build while using poetry.

**High-Level Project structure:**
This is a FastAPI-based application.
```
project
├─ .dockerignore
├─ .gitignore
├─ Dockerfile
├─ LICENSE
├─ Makefile
├─ README.md
├─ app
│  ├─ __init__.py
│  ├─ main.py
│  ├─ route
│  │  ├─ __init__.py
│  └─ service
│     ├─ __init__.py
│     └─ string_service.py
├─ gunicorn_config.py
├─ poetry.lock
├─ pyproject.toml
```

## Working with Plain Dockerfile

First, starting with a plain dockerfile, which we would normally write for deployment.

```Docker
FROM python:3.10.4-slim-buster

ARG POETRY_VERSION="1.1.13" \
    WORK_APP_DIR="/app" \
    APP_PORT=8080 

ENV PIP_DISABLE_PIP_VERSION_CHECK=on \
    POETRY_NO_INTERACTION=1 \
    POETRY_HOME="/opt/poetry" \
    POETRY_VERSION=$POETRY_VERSION \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1    

# for poetry command to work
ENV PATH="$POETRY_HOME/bin:$PATH"

RUN apt-get update \
    && apt-get install --no-install-recommends -y curl build-essential \
    && rm -rf /var/lib/apt/lists/*

# installing poetry
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -

WORKDIR $WORK_APP_DIR

COPY pyproject.toml poetry.lock ./

# installing python packages
RUN poetry export -f requirements.txt --without-hashes --output requirements.txt \
    && pip install --no-cache-dir -r requirements.txt

COPY . ./

EXPOSE $APP_PORT

CMD [ "make", "server" ]
```

Even, this format of dockerfile is new for me. Using different python flags, pip flags and poetry configuration through environment variables. As I am maintaining package dependencies through poetry, I am installing it first and exporting the dependencies as `requirements.txt` for installation. I could use poetry for installation as well, but as I will be working inside an isolated environment I won't need another virtual environment which is used by poetry.

`--no-cache-dir` for pip installation can also help to reduce image size, as it will avoid caching files for the future which is not necessary for docker image building.

This version of Dockerfile generates an image of size **581 mb**.

## Working with Docker Multi-Stage Build

Now, let's use multi-stage build to optimize the docker image.

```Docker
# Image for package building
FROM python:3.10.4-slim-buster AS builder

ARG POETRY_VERSION="1.1.13" \
    WORK_APP_DIR="/app"

ENV PIP_DISABLE_PIP_VERSION_CHECK=on \
    POETRY_NO_INTERACTION=1 \
    POETRY_HOME="/opt/poetry" \
    POETRY_VERSION=$POETRY_VERSION

# for poetry command to work
ENV PATH="$POETRY_HOME/bin:$PATH"

RUN apt-get update \
    && apt-get install --no-install-recommends -y curl \
    && rm -rf /var/lib/apt/lists/*

# installing poetry
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -

WORKDIR $WORK_APP_DIR

COPY pyproject.toml poetry.lock ./

# creating wheel for python packages
RUN poetry export -f requirements.txt --without-hashes --output requirements.txt \
    && pip wheel --no-cache-dir --wheel-dir $WORK_APP_DIR/wheels -r requirements.txt


# Image for deployment
FROM python:3.10.4-slim-buster

ARG APP_PORT=8080 \
    WORK_APP_DIR="/app"

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=on

RUN apt-get update \
    && apt-get install --no-install-recommends -y build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR $WORK_APP_DIR

COPY --from=builder $WORK_APP_DIR/wheels /wheels

# installing python packages
RUN pip install --no-cache-dir  /wheels/*

COPY . ./

EXPOSE $APP_PORT

CMD [ "make", "server" ]
```
This dockerfile will yeild a docker image of size **391 mb**. As we got rid of the unnecessary files in final stage, it results a smaller image size.
In the initial stage we download and build the wheels of our python packages. And this wheels are saved under a directory and can be used in the successive stages. Some files are necessary for building/compiling, but not for runtime. Getting rid of such files can save some space. We just imported the buit wheels in our final stage. We also didn't use `curl`, `poetry` in our final stage as we won't be needing those anymore.

## Conclusion

Using slimmer base image can also help to reduce docker image size for python applications, but as I mostly work ML project which has many dependencies, python `slim-buster` image helps a bit to avoid manul installation of some packages. This is not the most optimized dockerfile out there, this will work and can be modified for better performance. I will try to update this as I learn something new.


## References

- [Use multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)

- [Document docker poetry best practices #1879](https://github.com/python-poetry/poetry/discussions/1879)

- [python-poetry-docker-example](https://github.com/michaeloliverx/python-poetry-docker-example)

- [Python Poetry for Building Docker Images](https://binx.io/2022/06/13/poetry-docker/)

- [Python and Poetry on Docker](https://bmaingret.github.io/blog/2021-11-15-Docker-and-Poetry)
