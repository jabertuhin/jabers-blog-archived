---
layout: post
title: Developing inside a Container with VSCode
description: Instead of working on virtual environments, we can now use a docker container to work on our codes.
comments: false
image: images/architecture-containers.png
toc: true
categories: [Python, VSCode, Docker, English]
---
In this blog post, we will try to add a few files in our project folder, to start developing an existing project on the docker container with VSCode. We will be working on one of my python [pet project](https://github.com/jabertuhin/dvdrental-backend).

If you are in a hurry and just want to go through the code, here is the [github repo](https://github.com/jabertuhin/dvdrental-backend). Go through `.devcontainer` folder.

Let's start....


**Pre-requisites:** VSCode, Docker.

## Architecture

The architecture of VS Code working with docker container -

![]({{ site.baseurl }}/images/architecture-containers.png "Architecture [Image taken from their documentaion]")

We won't go into the details here. If you are interested, please read the [documentation](https://code.visualstudio.com/docs/remote/containers).

## Installing Extension

Install [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) from extensions marketplace.

## Create .devcontainer Folder

In our project, we need to create a `.devcontainer` folder. Here, we will keep our configuration files, which will be used to create our container for development purposes.
This folder and its' files will tell our recently installed extension to create a docker container and use it for development.

## Create Dockerfile

I have modified the `Dockerfile` a little bit, according to my need. Except that, this file was copied from [vscode-remote-try-python](https://github.com/microsoft/vscode-remote-try-python/blob/main/.devcontainer/Dockerfile). This definition will be used to create our development container.

```bash
ARG VARIANT=3.8-bullseye
FROM mcr.microsoft.com/vscode/devcontainers/python:0-${VARIANT}

# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="none"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# [Optional] If your pip requirements rarely change, uncomment this section to add them to the image.
COPY requirements.txt /tmp/pip-tmp/
COPY requirements.dev.txt /tmp/pip-tmp/
RUN pip3 --disable-pip-version-check --no-cache-dir install -r /tmp/pip-tmp/requirements.dev.txt \
   && rm -rf /tmp/pip-tmp
```

## Create devcontainer.json

Now, we have our dockerfile to create a container from it. But we might want to pass some build/runtime arguments for our need, we can configure those in this file. We can also add the necessary vscode extension ids that will be needed for our development. And VSCode will take care of all of our configured needs.

There is one thing that I want to add here. I have a locally running PostgreSQL database. I was struggling to connect my application(which is running inside a container) with the local database. Special thanks to **Sadman Amin**, for suggesting me to use `--network=host` as an argument, and this resolved the issue.
```json
{
	"name": "Python 3",
	"build": {
		"dockerfile": "Dockerfile",
		"context": "..",
		"args": {
			"VARIANT": "3.8-buster",
			"NODE_VERSION": "lts/*",
		}
	},
	"runArgs": ["--network=host"],

	// Set *default* container specific settings.json values on container create.
	"settings": {
		"terminal.integrated.profiles.linux": {
			"bash": {
				"path": "/bin/bash"
			}
		},
		"python.defaultInterpreterPath": "/usr/local/bin/python",
		"python.languageServer": "Default",
		"python.linting.enabled": true,
		"python.linting.pylintEnabled": true,
		"python.formatting.autopep8Path": "/usr/local/py-utils/bin/autopep8",
		"python.formatting.blackPath": "/usr/local/py-utils/bin/black",
		"python.formatting.yapfPath": "/usr/local/py-utils/bin/yapf",
		"python.linting.banditPath": "/usr/local/py-utils/bin/bandit",
		"python.linting.flake8Path": "/usr/local/py-utils/bin/flake8",
		"python.linting.mypyPath": "/usr/local/py-utils/bin/mypy",
		"python.linting.pycodestylePath": "/usr/local/py-utils/bin/pycodestyle",
		"python.linting.pydocstylePath": "/usr/local/py-utils/bin/pydocstyle",
		"python.linting.pylintPath": "/usr/local/py-utils/bin/pylint"
	},

	// Add the IDs of extensions you want installed when the container is created.
	"extensions": [
		"ms-python.python",
		"ms-python.vscode-pylance"
	],

	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	"forwardPorts": [8080],

	// Use 'portsAttributes' to set default properties for specific forwarded ports. More info: https://code.visualstudio.com/docs/remote/devcontainerjson-reference.
	"portsAttributes": {
		"8080": {
			"label": "DVD Rental",
			"onAutoForward": "notify"
		}
	},

	// Comment out to connect as root instead. More info: https://aka.ms/vscode-remote/containers/non-root.
	"remoteUser": "vscode"
}
```
There are other available properties of this JSON file that we can add, [for more info](https://code.visualstudio.com/docs/remote/devcontainerjson-reference).


## Preparing Container

We are done with our preparation, now we can tell VSCode to do its magic:

- Press <kbd>F1</kbd> and select the **Remote-Containers: Open Folder in Container...** command.
- Selecting the project folder will trigger the container to build and start.

## Git Question

In the beginning, I wasn't sure about the git configurations. How one might commit/push from a container.  Then I found my answer here [Working with Git?](https://code.visualstudio.com/docs/remote/containers#_working-with-git).


## Conclusion

The experience was quite good for me, and I enjoyed working on this. In my opinion, this will help team members to not worry about setting up the development environment on their workstation.
In a project, different team members use different OS on their workstations. Sometimes it becomes difficult to work with identical system configurations. This container based development might minimize such difficulties.

Finally, to get started with, I will suggest to clone and run [vscode-remote-try-python](https://github.com/microsoft/vscode-remote-try-python).


## Reference
- [Using Visual Studio Code from a docker image locally or remotely via VS Online](https://techcommunity.microsoft.com/t5/educator-developer-blog/using-visual-studio-code-from-a-docker-image-locally-or-remotely/ba-p/1264988)

- [VS Code Remote / GitHub Codespaces Container Definitions](https://github.com/microsoft/vscode-dev-containers)