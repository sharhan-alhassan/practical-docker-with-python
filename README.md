
*python -m venv venv*

## Docker
### 1. Docker as a technology
Docker provides an `OS` level virtualization known a `containers`. This is NOT the same as hardware virtualization.

### 2. Docker 101
Objectives:
- Install docker
- imagaes
- containers
- Dockerfile
- Docker compose

- `Docker image:` This is a `read-only` template that forms the foundation of your application. It can be seen as the `cooking recipe` that has the step-by-step instructions to cook a food. The Docker image usually starts with the `base-image` which can be the OS like Ubuntu or langugae like Python; using the `FROM` command. There are pre-built images for some common application stacks such as Django, `Ruby on Rails, PHP-FPM with nginx`, etc.

There are also slim packages to start with as the basics to  building other images. Some offered by Docker are `Alpine` and `Scratch`.

The series of commands used in creating a Docker image in the Dockerfile are called `instructions.` The built image can be published to a `registry`.

`Docker container:` It is the output process when a Docker image is run on a host system. One importaant thing to note is that, when a Docker container is stopped/killed all dat is lost. This means stateful applications are not recommended if their data will be lost any time the container is stopped/killed. This problem is remedied with `Docker volumes`. This provides persisten data storage even if the container is stopped/killed. 

`Bind Mounts and Volumes:` They are different storage drivers for data persistences. Some are: `volumes, bind mounts` and `tmpfs` volumes. `tmpfs` volumes are stored inn the host system's memory only, while `bind volumes` and `volumes` are stored in the host filesystem. 

`Docker registry:` This is a place where you can store Docker images so that they can be used as the basic for an applicatoin stack. Some examples of registries are:

    1. Docker Hub
    2. Google container registry
    3. Amazon elastic container registry
    4. JFrog artifactory

The visibility level of images can either be set to `public` or `private`. Public images can be seen by everone unlike private images. 

`Dockerfile:` A set of instructions to tell the Docker how to build the image. Some typical instructions are:

    FROM: instruction to tell what the base image is 

    ENV: instruction to pass an environment variable

    RUN: instruction to run some shell commands(eg; install dependent programs not available in the base image)

    CMD: or an ENTRYPOINT instruction that tells Docker which executable to run when a container is started. 

`Docker engine:` This is the core part of Docker. This is the `client-server` application that provides the platform, runtime, and tooling for managing Docker images, containers, etc. `Docker engine` provides the following:

    1. Docker daemon
    2. Docker CLI
    3. Docker API

- `1. Docker daemon:` The services that runs in the background the host computer and handles the heavy lifting of most of the docker commands

- `2. Docker CLI:` The primary way of interacting with the Docker with a set of commands. some common commands are:

    docker build 
    docker pull
    docker run 
    docker exec

Calling `help` before any CLI command will give the required documentation of the command. Example; if you want to see a brief documentation aobut `pull` and `image`

    docker help pull
    docker help image

`3. Docker API:` Provides a way of interacting with the docker engine. Almost all operations that are done with the Docker CLI can be done with the Docker API. The `curl` command is usually used. 

`Docker compose:` This is a powerful tool for running `multi-container` applications. Docker compose gives you the ability to build, launch and run multiple container applications including dependent and linked containers.
An example of a use case of Docker compose is to run applications with their dependent services( such as `databases` and `caching providers`)

Some docker commands:
- list images: `docker image ls`
- inspecting an image: `docker image inspect <image-name>`

### Let's play with a real image
We'll be pulling the `nginx` image. It is a popular reverse proxy server for HTTPS as well as a load balancer and a web server.

- Pull the nginx image: `docker pull nginx`

*Every image haa a associated tag. Without specifying it, it is set to the latest. Tags are there to easily roll back to previous versions*

You can decide to be specific and choose an image with the tag. For instance, let's pull a specifi nginx with a tag of `1.12-alpine-perl` version of nginx. You do this by appending the tag with a colon(`:`) after the image name (in this case `nginx`)

- `docker pull nginx:1.12-alpine-perl`

- By default, images are pulled from `docker hub`. In the event where you want to pull image fromm a differenct registry, apppend the prepend the name of the registry, colon, the port that it's been served, and the name of the image. Example, pulling an an nginx image from the registry `docker-local-registry` served on `port 4000`

    `docker pull docker-local-registry:4000/`
    
- If the registry needs authentication, you can do that:

    `docker login docker-local-registry:4000`

- To run a container:

    `docker run -p 80:80 nginx`

The `-p` flag is telling docker to `publish` the exposed port from the Docker container to the host

- To test if the server is working:

    `curl http://localhost:80`

- To stop the container

    `docker stop <container-id>`

- If the container refuses to stop, you can use the `kill` command

    `docker kill <container-id>`

- To confirm no container is running:

    `docker ps`

- `docker ps` only shows tha active/running containers. So to see or list all containers, use the command:

    `docker ps -a`

- You can remove a container by this command:

    `docker rm <container-id>` Example:
    `docker rm fadere34d5f`
    The ID pops up after success!
    `fadere34d5f`

- To confirm the container was indeed removed:

    `docker ps -a`

- Similarly, you can also remove an `image`:

    `docker rmi <image-id>`


*NB: In order to remove an image, all containers making reference to it must all be removed first else it'll throw an error*


### A Python Bot App for Telegram
- The Python app is a simple interface using Telegram Messenger to fetch the latest 10 stories from Reddit. 
- Using Telegram, we will subscribe to subreddits and the web application will check the subreddits for new posts.
- The new posts will be posted on the Bot interface which will then deliver the message to Telegram Messenger, when requested by the user. 
- Telegram's Bot creation interface is called `BotFather`
- Type BotFather in the search, type /newbot, type name, and username with `Bot` or `_bot` at the end
- Type the following the `token` to test if everything is working:

### Installing dependencies for Newsbot
- We need the following dependencies:
    1. praw - python reddit API wrapper
    2. request - for making standard HTTP requests

- `pip3 install -r requirements.txt`
- `-r` stands for required packages

    `curl https://api.telegram.org/bot<token>/getMe`


### Dockerfile:
- Dockerfile is an automated way to `build` Docker images. The Dockerfile contains series of steps required to build an image. 
An example Dockerfile instructions for a build process:

    FROM ubuntu:latest
    LABEL author="samhassan"
    LABEL description="sample dockerfile"
    RUN apt-get install python 
    COPY app.py
    CMD python app.py

### Dockerignore:
- Just like `gitignore`, it allows you to define files which are exempt from being transferred during the build process. 
- The ignore files are in `.dockerignore`
- Anything starting with `#` is a comment and ignored. 
- Example of a `.dockerignore` file:

    /temp
    .git
    # comment

- Starting a container from its image ID can be headache. It's helpful to add a `tag` to the image and rather use it in running

- create tag

    docker tag <image_id> <tag_name>
    docker tage 34feregrt34 sam:django-app

- In the build process use this:

    docker build -t sam:django-app

### Dockerfile Instructions:
- `FROM`: Tells docker engine which base image to use for subsequent instructions. It goes with a `tag`. If tag is not added, it takes the `latest` of the image. It's syntax

    FROM <image>
Or  
    FROM <image>:<tag>

- `WORKDIR`: This instruction sets the current working directory for `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD` instructions. Syntax is:

    WORKDIR pathto/directory

- `ADD` and `COPY`:
Allow you to transfer files from the host to the container's filesystem.
`COPY` supports basic copying of files to the container, while `ADD` has support for features like `tarball auto extraction` and `remote URL support`. 

Syntax for both instrunctions:

    ADD <source> <destination>
    COPY <source> <destination>

For dockerfiles that are built for Linux containers, an extra parameter `--chown` flag is added to change the owner/group of the files being added to the container.

Syntax: 

    ADD --chown=<user>:<group> <source> <destination>
    COPY --chown=<user>:<group> <source> <destination>

- `ADD` and `COPY` support wildcards too:
This command will copy all files with extension `.py` to the `apps` directory of the image

    COPY *.py apps

- `RUN`: It'll execute all commands in a new layer on top of the current image and create a new layer that is available for the next steps in the Dockerfile. 
    - Two forms of `RUN`:

    RUN <command> (know as the shell form)

    RUN ['executable', 'parameter 1', parameter 2'] (know as exec form)


- `CMD` and `ENTRYPOINT`: These instructions define which command is executed when running a container. Syntax for both are:

    CMD ["executable", "param1", "param2"] (exec form)
    CMD ["param1", "param2"] (as default parameters to ENTRYPOINT)

- `VOLUME`: This instruction tells Docker to create a directory on the host and mount it to a path specified in the instruction.
Example:

    VOLUME varlogs/nginx


- `EXPOSE`: Tells Docker that the container listens for the specified network ports at runtime. Syntax:

    EXPOSE <port> [<port>/<protocol>...]

    //To expose port 80
    EXPOSE 80

    //To expose port 8000 on TCP
    EXPOSE 8000/tcp







[Reference](https://www.amazon.com/Practical-Docker-Python-Release-Distribute-ebook/dp/B07FYZFKHQ)

