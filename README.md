## Contents
- [Setting Up Dockerfile](https://github.com/markuusche/docker-guide?tab=readme-ov-file#setting-up-dockerfile)
- [Running on Docker](https://github.com/markuusche/docker-guide?tab=readme-ov-file#running-on-docker)
- [Docker Build & Run](https://github.com/markuusche/docker-guide?tab=readme-ov-file#docker-build--run)
- [Remove | Delete Docker containers & images](https://github.com/markuusche/docker-guide?tab=readme-ov-file#remove-or-delete-docker-containers-or-images)
- [Logs](https://github.com/markuusche/docker-guide?tab=readme-ov-file#logging)


## Setting Up Dockerfile

"Assuming you already have a Dockerfile in your project"

Base image:
```
FROM python:latest
```
creates a folder named 'app':
```
RUN mkdir /app
```
cd to the created folder named 'app':
```
WORKDIR /app
```
copy all the files to /app using dot (.):
```
COPY . /app
```
apt-get: a linux command \
apt-get clean: removes the caches files after installing packages, freeing up space \
rm -rf /var/lib/apt/lists/*: removes the package lists downloaded by apt-get during the update process \
which is not needed anymore. Make sure to run this after `apt-get clean`
```
RUN apt-get update && apt-get install -y \
    tesseract-ocr \
    libtesseract-dev \
    libleptonica-dev \
    libgl1-mesa-glx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
```
install dependencies needed: \
--no-cache-dir: pip will download the packages, install them, and then immediately remove the downloaded files, saving disk space.
```
RUN pip install --no-cache-dir -r requirements.txt
```
execute or run the application with docker:
```
CMD ["pytest", "-vvvsq", "--headless"]
```
\
This is what the script would look like:
```
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
`.dockerignore` file is the same as .gitignore \
where you ignore the files you don't need to be in your repo or docker
  

  
## Running on Docker

#### [ Container ]
display the list of containers:
```
docker ps
```
display the list of containers including non-running containers:
```
dockers ps -a
```
#### [ Image ]
display the list of image:
```
docker image
```
display the list of all images including danglings: \
dangligs: untagged image
```
dockers images -a
```

## Docker Build & Run

#### [ Build ]
builds the project (Dockerfile) to an image: \
-t: means tag, then the tag name. In this example tag name is `py-image` \
dot (.) at the end of the command means \
docker will use the files and directories in the current directory (and its subdirectories) \
as the build context and build an image tagged as `py-image`.
```
docker build -t py-image .
```
#### [ Run ]
runs built project in the docker: \
-d: means background run.\
--name: name of the container `py-container` as an example. \
then `py-image` is the name of the Docker image that the container will be based on. 
```
docker run -d --name py-container py-image
```
in case if you have environment variables stored in a .txt file:\
it: means show the container output in real-time. (cannot use -d and -it at the same time) it will not log the output
```
docker run --env-file env.txt -it --name py-container py-image
```
if you are having and error like: _the input device is not a TTY.  If you are using mintty, try prefixing the command with 'winpty'_ \
use winpty prefix like this:
```
winpty docker run --env-file env.txt -it --name py-container py-image
```

## Remove or Delete docker containers or images

#### [ Container ]
prune: means delete or remove all docker containers \
-f: this flag means force. no need for y/N prompt.

```
docker container prune -f
```
rm: means remove (specific container) for ex. `docker-container`
```
docker rm docker-container
```

#### [ Image ]
prune: means delete or remove all docker containers \
rmi: means `remove image` (specific) for ex. `docker-image` \
-f: this flag means force. no need for y/N prompt. \
-a: this flag means all
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
how to log env values?: \
sampleEnvFile.txt: is where your ENV 'Keys' and 'values' for ex. `var=keys` no need to add ENV prefix \
docker-image-id: your docker-image-id, you can view it using `docker images` \
bash: this flag means you run in bash \
c: this flag means a command. for ex. your command is logging env value. in a normal bash terminal `echo $env` in docker `'echo "$env"'`
```
docker run --env-file sampleEnvFile.txt docker-image-id bash -c 'echo "$var"'
```
