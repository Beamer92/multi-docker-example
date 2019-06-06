# multi-docker-example

# Docker Workshop

## Setup

### Install
* Install docker desktop

### Create Accounts
* Dockerhub 
* Travis CI
* Github

### Clone 
* https://github.com/rogerwschmidt/multi-docker-example


## Objectives
* Docker Basics
* Custom Docker Images
* Docker Compose
* Production-Grade Workflow
* Deployment to AWS


## Docker Basics
* Docker basics, images v. containers

#### To get an image from DockerHub
`docker pull redis`

#### To view all local images
`docker images`

#### To view current running containers
A container is a image that has been started with the `run` command

`docker ps`

#### To view all containers, started and stopped

`docker ps --all`

Once a container has been stopped, it can be restarted with 

`docker start <container-id>`

#### To stop a running container

`docker kill <container-id>`

## Custom Docker Images

In a folder of your choice, create a file named `Dockerfile`, add the following

```
# Use an existing docker image as a base
FROM alpine


# Download and install a dependency
RUN apk add --update redis

# Tell the image what to do when it starts
# as a containers
CMD ["redis-server"]
```

#### To build the image
`docker build .`

#### To create and run a container from the image
`docker run <image-id-from-build-process>`

You can stop the container with `CTRL+C`.

#### Accessing a running container
* Start a docker container 

`docker run -d <image-id-from-build-process>`

`-d` will start the container in detached mode, you'll get back the command line

* Attach to a running container

`docker exec -it <container-id> /bin/bash`

### Using Volumes

In the repo, change directory to `client`, then build image

`docker build -f Dockerfile.dev .`

The `-f` flag allows you to give a specific file to build from

`docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image id>`

Will use volumes such that the binaries will be used from the container, but the code will be used from the file system.

## Docker Compose

Docker compose is used to setup a development environment.

`docker-compose up`

#### Topics
* Services
* Service names
* Build v. Images
* Volumes
* Environment variables
* Service Dependencies

## Production-Grade Workflow

### Travis CI

Travis CI is a continuous integration tool that will look for pushes in github, and trigger a build process

### CI
* sudo and services
* before_install
* scripts
* after_success
  * failures here won't fail the build

### Creating Dockerfiles
* client
* nginx
* server
* worker


### Push to Docker Hub
* Travis CI Environmental variables
* Logging in to Docker Hub
* Pushing to Docker hub

## Deployment to AWS

* AWS Elastic Beanstalk application and environment creation
* Travis CI configuration
* Dockerrun configuration
* Redis and Postgres Provisioning
* Elastic Beanstalk environment variables
* Example of full workflow

