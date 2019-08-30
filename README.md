# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) DevOps - Docker and Kubernetes

## Objectives

- Understand Docker and containers
- Explain the origin of Docker and why it's a useful technology
- Demonstrate how to pull an image from Docker Hub and run a container on your machine
- Explain common Docker CLI commands
- Understand how to run source code as a container volume
- Demonstrate how to push a Docker image to production in Heroku
- Understand Kubernetes and container orchestration

<br>

## Introduction to Docker

![whales!](https://www.brianweet.com/assets/docker-blog-1/docker-logo.png)


Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package.

Summary... Develop your application and its supporting components using containers.
The container becomes the unit for distributing and testing your application.

Docker is written in Go and runs on Linux and Windows servers. It runs on Mac as a virtual machine Docker Client.

#### The main components of Docker

- **Image** - A blueprint to build a container.
	- Layered File System - Images are built of read only layers which may contain files for OS, files from framework, files for app.
	- Dessert - base layer and you stack layers on top. Layers are files that stck on top of each other
	- Once an image is baked it's read only, a container has a thin r/w layer. When container is deleted this layer is deleted.
	- Containers can share image layers, makes it very efficent. But they each have a unique r/w layer.
- **Container** - An isolated instance created from an image, runs the app on a linux or windows machine.
	- You can start/stop a container easily.
	- Checks a cache for exisiting layers.

### The Old Way

"It works on my machine"

![https://i-msdn.sec.s-msft.com/dynimg/IC372328.png](https://i-msdn.sec.s-msft.com/dynimg/IC372328.png)

> In the olden days of [Web 2.0](https://en.wikipedia.org/wiki/Web_2.0) there was a huge difference between the machine that a developer used to build an app and what was used to deploy said app. It took days, if not weeks to `provision` or build a computer with the right hardware and software to deploy an app. More often than not an app that was built on a developer's machine (often a `mac`) did not work properly in production (usually a `linux` machine). Reasons could range from different dependencies that were required by the app or versioning issues.

### Short lived new way

Virtual Machines

>In computing, a virtual machine (VM) is an emulation of a computer system. Virtual machines are based on computer architectures and provide functionality of a physical computer. Their implementations may involve specialized hardware, software, or a combination.

Basically "fakes" a physical representation of a machine on another machine

- Docker is not the same as a VM. 
- A VM has a copy of the OS for each. 
- A Hypervisor is needed (expensive).

The Docker Engine integrates with the OS to run the Docker containers. Sits directly on top of the OS. It's very fast and inexpensive.

![](https://i.imgur.com/g2qbvq0.png)

### Some virtual Machines

- [https://jsnes.fir.sh/](https://jsnes.fir.sh/)
- [http://openemu.org/](http://openemu.org/)
- [https://www.vagrantup.com/](https://www.vagrantup.com/)

VM Pros:

- Complete isolation (sandbox)

VM Cons:

- Slow
- Takes up a lot of space
- Still annoying to deploy

<br>

## Docker uses existing Linux features

Docker isn't a completely new technology. Many of the components and principles existed previously. Docker is written in Go and takes advantage of several features of the Linux kernel to deliver its functionality.

[Docker Underlying Technology](https://docs.docker.com/engine/docker-overview/#the-underlying-technology)

#### Namespaces

- Limits what you can see. Provides processes (a container) with it's own view of the system.
- Allows for isolation/sandboxing
- Try the `ps aux` or `ps -ef` command on the host and container for an example

[Namespaces Wiki](https://en.wikipedia.org/wiki/Linux_namespaces)

> Namespaces are a feature of the Linux kernel that partitions kernel resources such that one set of processes sees one set of resources while another set of processes sees a different set of resources. The feature works by having the same name space for these resources in the various sets of processes, but those names referring to distinct resources. Examples of resource names that can exist in multiple spaces, so that the named resources are partitioned, are process IDs, hostnames, user IDs, file names, and some names associated with network access, and interprocess communication.

#### Control Groups (cgroups)

- Limits how much you can use per container
- Controls and monitors resource utilization

[cgroups](https://en.wikipedia.org/wiki/Cgroups)

Simplifies building shipping and running apps

> A control group (abbreviated as cgroup) is a collection of processes that are bound by the same criteria and associated with a set of parameters or limits. 


#### Union file systems

- Union file systems, or UnionFS, are file systems that operate by creating layers, making them very lightweight and fast. Docker Engine uses UnionFS to provide the building blocks for containers. Docker Engine can use multiple UnionFS variants, including AUFS, btrfs, vfs, and DeviceMapper.
- With VMs you must make a transfer of the entire app
- With Docker you only transfer the new layer, does a diff
- CoW (Copy on Write) - mounts images as read only inside each container. 
	- It doesn't clone
	- Each container has a unique read/write layer

<br>

## Summary - Why should we use Docker/containers?

### Benefits

- Set up environnment very quickly
	- challenging to set up everything on a new devs individual machines 
- Eliminate app conflicts and version conflicts
	- create containers with seperate versions for updates
- environment consistency between dev, stage, prod
- ship code faster with predictability 
- microservices
- Also handy if you want to try out a new tech without installing crap you won't want


### Goal

We want to mirror our dev environment with our production environment. This is mostly useful for your `back-end` but can applied to your front-end applications as well! It's cool to run docker locally but the real benefit of docker is to run it on some production machine.

![https://blog.octo.com/wp-content/uploads/2014/01/Diapositive1.png](https://blog.octo.com/wp-content/uploads/2014/01/Diapositive1.png)

<br>

## Setup Environment



#### Docker Install

- [Download Docker](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
- [Orientation and setup](https://docs.docker.com/get-started/)


Check out preferences

- start on startup
- advanced tab

Docker CLI

- `docker` - man pages
- `docker images` - lists the local images

<br>

### Docker Tools

- Docker Client - push/pull images
- Docker Compose - orchestrate multiple containers
- Docker Kitematic - GUI to run containers

<br>

## Docker Client

The command line interacts with docker daemon, the engine that runs containers.

![](https://i.imgur.com/aZuu2mc.png)

![](https://i.imgur.com/9OQUMov.png)


[Docker API Docs](https://docs.docker.com/engine/api/v1.30/#)

> The Engine API is an HTTP API served by Docker Engine. It is the API the Docker client uses to communicate with the Engine, so everything the Docker client can do can be done with the API.

> Most of the client's commands map directly to API endpoints (e.g. `docker ps` is `GET /containers/json`). The notable exception is running containers, which consists of several API calls.

Build and manage images, run and manage containers

- `docker pull`
- `docker run`
- `docker images` - lists images
- `docker ps -a` - lists all containers

`docker` - shows man pages

<br>

## Docker Kitematic

- [Kitematic](https://kitematic.com/)
- [Kitematic User Guide](https://docs.docker.com/kitematic/userguide/)


A quick and easy GUI interface to search for images and run containers.

![](https://i.imgur.com/fWHGzWi.png)

<br>

## Docker Hello World Image

Let's test that our machine is working properly by pulling and running a hello world image locally in a container.

Let's go to the Hello World docs on Docker Hub:

- [Docker Hub](https://hub.docker.com/)
- [Hello World Image](https://hub.docker.com/_/hello-world)


1. `docker pull hello-world` - Pulls down the layered file system
1. `docker images` - Confirm that the image was downloaded
1. `docker run hello-world` - will create and run a container from hello-world
1. `docker ps -a` - lists all containers running and not running
1. `docker stop xxx` - The first few characters will stop a ruinning container (Hello World auto exits)
1. `docker rm xxx` - first few characters of container will remove it
1. `docker rmi xxx` - first few characters of image will remove layered file system

<br>

## Run a Docker container and open a port

For this example we'll pull and run a Hello World ngnix image. [ngnix](https://www.nginx.com/) can be used as a load balancer, microservices manager, caching server, or API gateway.

[Docker Hub nginx Image](https://hub.docker.com/r/kitematic/hello-world-nginx)

1. `docker pull kitematic/hello-world-nginx`
1. `docker images` - Confirm that the image was downloaded
1. Let's run the image in a container, but this time we'll assign a port from our local machine to the running container.
	- `docker run -p LOCALPORT:DOCKERPORT IMAGEID` 
	- `docker run -p 80:80 03b4557`
1. Navigate to `localhost:80` in your browser to view the running container.
1. `docker ps -a` - lists all containers running and not running
1. `docker stop xxx` - The first few characters will stop a ruinning container (Hello World auto exits)
1. `docker rm xxx` - first few characters of container will remove it
1. `docker rmi xxx` - first few characters of image will remove layered file system

<br>


## Run Docker Container in Background (detached)


[Docker run reference](https://docs.docker.com/engine/reference/run/)

Use the `-d` flag to run in the background

- `docker run -d -p 80:80 03b4557`

#### Docker Logs

[Docker Logs Docs](https://docs.docker.com/engine/reference/commandline/logs/)

- `docker logs [OPTIONS] CONTAINERID`

#### Prune

- `docker container prune`
- `docker system prune`

<br>

## Hook Source code to a container with Volumes

#### Volumes

- Volume - special directory in a container, can be shared
	- Like an external HD or USB drive
	- Persists when container is deleted
	- Updates to an image won't affect a data volume.

- You can let Docker figure it out `/var/www` (an alias) - great for logs and stuff you dont necessary access a lot, you can specify a mounted folder on the Docker host 

#### We Do: Pull `alpine-node` Docker image

We're gonna use a very lightweight image of node called [alpine-node](https://hub.docker.com/r/mhart/alpine-node).

- `docker pull mhart/alpine-node`

#### We Do: Pull an Express app

[Fork and Clone this express app](https://github.com/marcwright/bonobos-express-app)


```sh
docker run -p 3000:3000 -v $(pwd):/var/www -w "/var/www" IMAGEID npm start
```

- `$(pwd)` sets current directory that you've cd into
- `-w` tells docker where to run the npm start command, otherwise it won't know
- runs on `localhost:3000`
- Locate a volume: `docker inspect mycontainer`

Note - once you stop the container you will lose changes unless you build an image.

#### Interactive Terminal Mode

- `docker run -it`
- The alpine node image does not have bash 
- `docker exec -it CONTAINERID sh`

#### Removing Container and Volumes

- `docker rm -v CONTAINERID` 
- mainly helpful when you let docker manage the volume for logging, etc

<br>

## Linux `etc/hosts`

[Docker Configure DNS](https://docs.docker.com/v17.09/engine/userguide/networking/default_network/configure-dns/)

Good for testing CORS issues


`docker inspect CONTAINER`

`-h HOSTNAME or --hostname=HOSTNAME`

> Sets the hostname by which the container knows itself. This is written into /etc/hostname, into /etc/hosts as the name of the container's host-facing IP address, and is the name that /bin/bash inside the container will display inside its prompt. But the hostname is not easy to see from outside the container. It will not appear in docker ps nor in the /etc/hosts file of any other container.

<br>



## Dockerfile Intro

- Text file with instructions
- Run through `docker build`
- Creates a docker image
- `touch Dockerfile` in the root of our app

Key instructions

- `FROM`: base
- `MAINTAINER`
- `RUN`: npm installs, etc
- `COPY`: copy source code into container
- `ENTRYPOINT`: kicks everything off (heroku needs CMD)
- `WORKDIR`: where to run the `RUN` commands, point of reference
- `EXPOSE`: port (Heroku doesn't need this)
- `ENV`
- `VOLUME`

```docker
FROM mhart/alpine-node:latest

LABEL author="Marc Wright"

ENV NODE_ENV=production
ENV PORT=3000

COPY . /var/www
WORKDIR /var/www

RUN npm install

EXPOSE $PORT

CMD [ "npm", "start" ]
```

#### Publish to docker hub

- `docker login`
- `docker build -t USERNAME/IMAGENAME .`
- `docker push USERNAME/IMAGENAME`

<br>


## Running containers on Heroku

- [Heroku Buildpack](https://devcenter.heroku.com/articles/buildpack-builds-heroku-yml)
- [Heroku Heroku.yml](https://blog.heroku.com/build-docker-images-heroku-yml)
- [Heroku Container Regristry](https://github.com/heroku/heroku-container-registry)
- [Heroku Container Regristry and Runtime](https://devcenter.heroku.com/articles/container-registry-and-runtime)


> ENTRYPOINT is optional. If not set, /bin/sh -c will be used
CMD is required. If CMD is missing, the registry will return an error
CMD will always be executed by a shell so that config vars are made available to your process; to execute single binaries or use images without a shell please use ENTRYPOINT


1. Log in to Container Registry:
```
heroku container:login
```
1. Navigate to the app’s directory and create a Heroku app:
```
heroku create
```
1. Build the image and push to Container Registry:
```
heroku container:push web
```
1. Then release the image to your app:
```
heroku container:release web
```
1. Now open the app in your browser:
```
heroku open
```

<br>

## Circle CI 

[Using Custom-Built Docker Images on Circle CI](https://circleci.com/docs/2.0/custom-images/)

<br>

## Kubernetes

[What is Kubernetes?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

> Kubernetes is a portable, extensible open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

Google open-sourced the Kubernetes project in 2014. Kubernetes builds upon a decade and a half of experience that Google has with running production workloads at scale, combined with best-of-breed ideas and practices from the community.


- Docker Swarm: Orchestrates multiple containers on a single host
- Kubernetes: Orchestrates containers spread accross multiple hosts.

#### Key Terms

##### Master Components
- Control a cluster, start/stop new containers, scheduling

##### Node Components
- Node components run on every node, maintaining running pods and providing the Kubernetes runtime environment.
- Can be a series of pods

##### Pod
- A single instance of an application in Kubernetes, which might consist of either a single container or a small number of containers that are tightly coupled and that share resources.
- Docker is the most common container runtime used in a Kubernetes Pod, but Pods support other container runtimes as well.




<br>


## Additional Resources

- [Intro to Docker (16mins)](
https://acloud.guru/course/docker-fundamentals/learn/1e1626a5-01b2-c3ff-6abb-d92dd6b5e280/1aa93e70-a8e6-ccef-eb97-41b3e03bccaa/watch)
- [A Cloud Guru](https://acloud.guru/course/docker-fundamentals/dashboard)
- [Cloud Academy](https://cloudacademy.com/amazon-web-services/labs/start-with-docker-linux-aws-109/)
- [Dockerizing a node app](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
- [Sharing Volume](https://github.com/rocker-org/rocker/wiki/Sharing-files-with-host-machine)
- [Env Variables Docker](https://stackoverflow.com/questions/30494050/how-do-i-pass-environment-variables-to-docker-containers)
- [Postgres](https://www.godaddy.com/garage/how-to-install-postgresql-on-ubuntu-14-04/)
- [AWS ECS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html)
- [Docker on Ubuntu](https://askubuntu.com/questions/909691/how-to-install-docker-on-ubuntu-17-04)
- [Creating a stack](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-cli-creating-stack.html)
- [Getting Started](https://docs.docker.com/get-started/#setup)
- [Debugging Docker](https://medium.com/@betz.mark/ten-tips-for-debugging-docker-containers-cde4da841a1d)

<br>

<details>
<summary>EXTRA TIME: BUILD CUSTOM IMAGES</summary>
## Building Custom Images with Dockerfile

How do you get source code into a container?


## Build a Node.js Image

`docker build -t <your username>/node .`

- --tag
- . is folder we're building from, build context

delete node_modules since the file will do that anyway in `RUN`

`docker build -f Dockerfile -t marcwright/node .`

It looks for Dockerfile anyway.

Notice the intermediate container lines, it's like source control, it's cached and looks for changes

</details>



<details>
<summary>EXTRA TIME: COMMUNICATE BETWEEN CONTAINERS</summary>

# Communicate between containers

## Container Legacy Linking

- Run a container with a name `--name`
	- `docker run -d --name my-postgres postgres`
- Link to running container `--link`
	- `docker run -d -p 5000:5000 --link my-postgres:postgres marcwright/node`
	- name of container - my-postgres, aliais inside other container is postgres

- `docker exec` run a command in a running container
	- to seed for example - `docker exec CONTAINERID node dbSeeder.js`

- To link to database, in config file, the `host` is the name of the db container
	
## Link Node to Mongo

## Add Containers to a bridge network (newer)

- Create custom bridge network
	- `docker network create --driver bridge isolated_network` 
- Run Containers in the network
	- `docker run -d --net=isolated_network --name mongodb mongo` 

- `docker network inspect isolated_network` - note that there are no containers communicating with it
- `docker run -d --net=isolated_network --name schmitty -p 8080:3001 -v$(pwd):/var/www -w "/var/www" mhart/alpine-node npm start`

</details>

<details>
<summary>EXTRA TIME: DOCKER COMPOSE</summary>

# Manage Containers with Docker Compose

Great way to automatically manage the lifecycle of your application. Manage multiple containers.

- Start, stop and rebuild services
- View status of running services
- stream logs
- run a one off command on a service

#### Need

NGINX to 3 node servers call to mongo and cache in redis

#### Docker Compose Workflow

- Build services
- startup services
- tear down services

## `docker-compose.yml`

- service configuration
- docker compose build -> creates images (services) to create containers

```
version: '2'
services: 
	node:
		build:
			context: .
			dockerfile: node.dockerfile
		networks:
	//define what we want to be running
	// Node, DBs, caching
	build: context
	environment:
	image:
	networks:
	ports:
	volumes:
	
```

## Compose Commands

- `docker-compose build`
	- build or rebuild services defined in .yml file
- `docker-compose build mongo`
	- one-off rebuild 
- `docker-compose up`
	- create and start the containers
- `docker-compose down`
	- stop and remove 
	- `--rmi all --volumes` delete images and volumes
- `docker-compose down`

</details>

<br>
