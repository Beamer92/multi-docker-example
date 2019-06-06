# multi-docker-example

# Docker Workshop

## Setup

### Install
* Install docker desktop

### Create Accounts
* Dockerhub 
* Travis CI
* Github

## Objectives
* Docker Basics
* Custom Docker Images
* Docker Compose
* Production-Grade Workflow
* Deployment to AWS



## Docker Basics
* Docker basics, images v. containers

`docker pull redis`


## Custom Docker Images
## Docker Compose
## Production-Grade Workflow



## Deployment to AWS

* AWS Elastic Beanstalk application and environment creation
* Travis CI configuration
* Dockerrun configuration
* Redis and Postgres Provisioning
* Elastic Beanstalk environment variables
* Example of full workflow



## Production-Grade Workflow

Worflow is defined as 
* Development
* Testing
* Deployment

## Development workflow
* Using git with 2 branches
  * Feature branch
  * Master branch, automatically deployed
* Use a pull request on github to merge your changes
  * Which will trigger a buch of activities
  * Travis CI for testing
  * Travis will push to AWS (Elastic Bean stalk)
  
Docker is not necessary to do the workflow, but it makes things easier

Creating a simple react application 

`create-react-app`
In development -> npm run start
in production -> npm run build

Create Dockerfile.dev
Create .dockerignore
docker build -f Dockerfile.dev .
docker run -p 3000:3000 <image id>
  
in the browser go to localhost:3000

Make a change to the `src/App.js`. Is the change reflected in the browser?
We do not want to rebuild the image everytime the source code is rebuilt

Rebuild image
To use volumes use the `-v` command
delete `node_modules`
docker run -p 3000:3000 -v $(pwd):/app <image id>
this will fail because there is a node_modules in the containter, but not in the filesystem
  
docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image id>
-v with out the colon, will not map from containter to filesystem


docker-compose can be used to handle a single container
```
version: '3'

services: 
  web:
    build:
      # where to look for files to build
      context: . 
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app
```
### Testing
Testing can be a bit weird using Docker, lets take a look at `test.sh` and see what it's doing.

### Production
For production code, there is not need for all the development tools, all is needed is nginx to serve the index.html 
and the main.js file

We only care about the build folder, nothing is required. We are going to use a multiphase build process

Build phase gets thrown away
## Build Phase
use node:alpine
copy package.json
install deps
npm run build

## Run Phase
use nginx
copy `/build` folder
start nginx

https://hub.docker.com/_/nginx/

to run Dockerfile `docker build .`
docker run -p 8080:80 <image id>

go to localhost:8080

## Continuous integration and deployment with AWS
Github to TavisCI to AWS

Login to Travis-CI
Go to Account Settings
Accept Github App Integration
add `docker-react-example`
Click on Settings, its small box on the right side of the screen

Turn off pushed branches
Turn off pushed pull requests
Add travis config
push changes to github
you might need to trigger build



Go to AWS
elastic beanstalk
create new application
create environment -> Web server

Select docker as the platform 
then create environment
this will take a bit of time

Add `deploy` to travis config
get s3 bucket_name
get api keys from IAM
click add user on the left
name user `docker-react-example-travis-ci`
select access type `programmatic access`
hit next
select `Attach existing policies directly`
filter policies by `beanstalk`
check the one that has the description of `Provides full access to AWS Elastic Beansta...`
Select next: Tags
select next: Review
select Create user

Now you should see the API keys that can be used to deploy to AWS
The keys should not go into git hub

In travis, add the keys as environment variables
add, commit, and push changes to `.travis.yml`
watch for travis to run build

Once build and deploy is complete, got to the elastic beanstalk page for your application
and wait for elastic beanstalk to update the environment.



=========================================================
Lets create docker files for development for client, server, and worker

### client
Dockerfile.dev
```
FROM node:alpine

WORKDIR '/app'
COPY ./package.json ./
RUN npm install 
COPY . .

CMD ["npm", "run", "start"]
```

docker build -f Dockerfile.dev .
docker run -p 3000:3000 <image id>

### server
```
FROM node:alpine

WORKDIR "/app"

COPY ./package*.json ./
RUN npm install
COPY . .
CMD [ "npm", "run", "dev"]
```


docker build -f Dockerfile.dev .
docker run -p 3000:3000 <image id>

### worker
```
FROM node:alpine

WORKDIR "/app"

COPY ./package*.json ./
RUN npm install
COPY . .
CMD [ "npm", "run", "dev"]
```


docker build -f Dockerfile.dev .
docker run -p 3000:3000 <image id>

## adding docker-compose to project
Add nginx config
Create nginx Dockerfile
create docker-compose.yml

## create production docker files
create new docker files for
* worker
* server
* nginx
  * could have an nginx production configuration
  
* client
  * use multistage build process
  * uses port 3000, which needs configuration
  
push all changes to github

## travis CI
Add repo to travis CI
look over travis CI config
add dockerhub credentials as environment variables


## publishing 
dockerrun.aws.json will tell elastic beanstalk what to do
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html


there is not postgres or redis in dockerrun, why?


