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
-t: means tag, then the tag name \
in this example tag name is `py-image` \
dot (.) at the end of the command means \
docker will use the files and directories in the current directory (and its subdirectories) \
as the build context and build an image tagged as `py-image`.
```
docker build -t py-image .
```
#### [ Run ]
runs built project in the docker:
-d: means background run.\
--name: name of the container `py-container` as an example. \
then `py-image` is the name of the Docker image that the container will be based on. 
```
docker run -d --name py-container py-image
```


