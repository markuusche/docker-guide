> [!NOTE]
> This README contains personal notes and hints intended for future reference. 

## Contents
- [Setting Up Dockerfile](https://github.com/markuusche/docker-guide?tab=readme-ov-file#setting-up-dockerfile)
- [Running on Docker](https://github.com/markuusche/docker-guide?tab=readme-ov-file#running-on-docker)
- [Docker Build & Run](https://github.com/markuusche/docker-guide?tab=readme-ov-file#docker-build--run)
- [Remove | Delete Docker containers & images](https://github.com/markuusche/docker-guide?tab=readme-ov-file#remove-or-delete-docker-containers-or-images)
- [Logs](https://github.com/markuusche/docker-guide?tab=readme-ov-file#logging)


## Setting Up Dockerfile

"Assuming you already have a Dockerfile in your project"

base image:
```dockerfile
FROM python:latest
```
creates a folder named 'app':
```dockerfile
RUN mkdir /app
```
cd to the created folder named 'app':
```dockerfile
WORKDIR /app
```
copy all the files to /app using dot (.):
```dockerfile
COPY . /app
```
download & install the needed pre-requisite(s) into docker
```dockerfile
RUN apt-get update && apt-get install -y \
    tesseract-ocr \
    libtesseract-dev \
    libleptonica-dev \
    libgl1-mesa-glx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
```
![](https://img.shields.io/badge/apt--get-blue?style=flat-square&color=rgba(0,0,255,0.5)) \
![](https://img.shields.io/badge/linux%20command-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/apt--get%20clean-blue?style=flat-square&color=rgba(0,0,255,0.5))\
![](https://img.shields.io/badge/removes%20the%20caches%20files%20after%20installing%20packages%2C%20freeing%20up%20space-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/rm%20--rf%20%2Fvar%2Flib%2Fapt%2Flists%2F%2A-blue?style=flat-square&color=rgba(0,0,255,0.5))\
![](https://img.shields.io/badge/removes%20the%20package%20lists%20downloaded%20by%20apt--get%20during%20the%20update%20process%0Awhich%20is%20not%20needed%20anymore.%20Make%20sure%20to%20run%20this%20after%20apt--get%20clean-blue?style=flat-square&color=rgba(0,0,0,0))

install dependencies needed:
```dockerfile
RUN pip install --no-cache-dir -r requirements.txt
```
![](https://img.shields.io/badge/----no--cache--dir-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/pip%20will%20download%20the%20packages%2C%20install%20them%2C%20and%20then%20immediately%20remove%20the%20downloaded%20files%2C%20saving%20disk%20space.-blue?style=flat-square&color=rgba(0,0,0,0))


execute or run the application with docker:
```dockerfile
CMD ["pytest", "-vvvsq", "--headless"]
```
\
This is what the script would look like:
```dockerfile
FROM python:latest

RUN mkdir /app

WORKDIR /app

RUN mkdir -p screenshots/decoded \
    mkdir -p logs

COPY . /app

RUN apt-get update && apt-get install -y \
    tesseract-ocr \
    libtesseract-dev \
    libleptonica-dev \
    libgl1-mesa-glx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir -r requirements.txt

CMD ["pytest", "-vvvsq", "--headless"]
```

#### [ .dockerignore ]
![](https://img.shields.io/badge/.docker%20ignore-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/ignore%20the%20files%20you%20don%27t%20need%20in%20your%20docker-blue?style=flat-square&color=rgba(0,0,0,0))
  
  
## Running on Docker

#### [ Container ]
display the list of containers:
```bash
docker ps
```
display the list of containers including non-running containers:
```dockerfile
dockers ps -a
```
#### [ Image ]
display the list of image:
```dockerfile
docker image
```
display the list of all images including danglings:
```dockerfile
dockers images -a
```
![](https://img.shields.io/badge/danglings-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/untagged%20image-blue?style=flat-square&color=rgba(0,0,0,0))

## Docker Build & Run

#### [ Build ]
builds the project (Dockerfile) to an image:
```docker
docker build -t py-image .
```
![](https://img.shields.io/badge/--t-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/means%20tag%2C%20then%20the%20tag%20name.%20In%20this%20example%20tag%20name%20is%20py--image-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/dot%20%28.%29-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/at%20the%20end%20of%20the%20command%2C%20docker%20uses%20the%20current%20directory%20%28and%20its%20subdirectories%29%20as%20the%20build%20context%20to%20build%20an%20image%20tagged%20as%20py--image-blue?style=flat-square&color=rgba(0,0,0,0))

#### [ Run ]
runs built project in the docker:
```dockerfile
docker run -d --name py-container py-image
```
![](https://img.shields.io/badge/--d-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/background%20run-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/----name-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/name%20of%20the%20container%20py--container%20as%20an%20example%2C%20then%20py--image%20is%20the%20name%20of%20the%20docker%20image%20that%20the%20container%20will%20be%20based%20on.-blue?style=flat-square&color=rgba(0,0,0,0))

in case if you have an environment variables stored in a .txt file:
```dockerfile
docker run --env-file env.txt -it --name py-container py-image
```
![](https://img.shields.io/badge/--it-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/show%20the%20container%20output%20in%20real--time.%20%28cannot%20use%20--d%20and%20--it%20at%20the%20same%20time%29%20it%20will%20not%20log%20the%20output-blue?style=flat-square&color=rgba(0,0,0,0))

if you are having and error like: _the input device is not a TTY.  If you are using mintty, try prefixing the command with 'winpty'_ \
use winpty prefix like this:
```bash
winpty docker run --env-file env.txt -it --name py-container py-image
```

## Remove or Delete docker containers or images

#### [ Container ]
prune: means delete or remove all docker containers
```dockerfile
docker container prune -f
```
![](https://img.shields.io/badge/--f-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/this%20flag%20means%20force.%20no%20need%20for%20y%2FN%20prompt.-blue?style=flat-square&color=rgba(0,0,0,0))

```
docker rm docker-container
```
![](https://img.shields.io/badge/rm-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/remove%20%28specific%20container%29%20for%20ex.%20docker--container.-blue?style=flat-square&color=rgba(0,0,0,0))

#### [ Image ]
```
docker rmi docker-image
```
remove all dangling images:
```
docker image prune
```
force and remove all including dangling images:
```
docker image prune -a -f
```
![](https://img.shields.io/badge/prune-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/delete%20or%20remove%20all%20docker%20containers-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/rmi-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/remove%20specific%20image%20or%20ex.%20docker--image-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/--f-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/this%20flag%20means%20force.%20no%20need%20for%20y%2FN%20prompt.-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/--a-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/this%20flag%20means%20all%0A-blue?style=flat-square&color=rgba(0,0,0,0))

## Logging

#### [ Shows Logs ]
shows the logs of the executed script or application using container name in this example `docker-container` \
this is same as terminal logs.
```
docker logs -f docker-container
```
using container id in this example `ac5a630a2df36c4c276e5e5cb`
```
docker logs -f ac5a630a2df36c4c276e5e5cb
```
how to log env values?:
```
docker run --env-file sampleEnvFile.txt docker-image-id bash -c 'echo "$var"'
```
![](https://img.shields.io/badge/sampleEnvFile.txt-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/is%20where%20your%20ENV%20%27Keys%27%20and%20%27values%27%20for%20ex.%20%60var%3Dkeys%60%20no%20need%20to%20add%20ENV%20prefix-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/docker--image--id-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/your%20docker--image--id%2C%20you%20can%20view%20it%20using%20%60docker%20images%60%20-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/bash-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/run%20in%20bash-blue?style=flat-square&color=rgba(0,0,0,0))\
![](https://img.shields.io/badge/--c-blue?style=flat-square&color=rgba(0,0,255,0.5))
![](https://img.shields.io/badge/means%20a%20command.%20for%20ex.%20your%20command%20is%20logging%20env%20value.%20in%20a%20normal%20bash%20terminal%20echo%20%24env%20in%20docker%20%27echo%20%22%24env%22%27%27-blue?style=flat-square&color=rgba(0,0,0,0))


