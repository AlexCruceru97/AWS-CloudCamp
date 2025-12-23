# Week 1 — App Containerization

## Containterization
Locally using docker I create an container called "BootCampProject"(name can be seen in devcontainer.json). Which will now will become my workspace/windows. Here I can launch other containers, for example container for flask application, node js apps etc.
Using 
```
"features": {
		"ghcr.io/devcontainers/features/docker-in-docker:2":{},
		"ghcr.io/devcontainers/features/python:1": {
        "version": "3.10" 
		},
		"ghcr.io/devcontainers/features/node:1": {
            "version": "18"
        } 
	},
```
allows me to use docker commands inside the container BootCampProject, or install python, npm, etc, how I am doing inside Windows.
```
┌─────────────────────────────────────────────────────────┐
│ Windows (Physical Machine)                              │
│ - "The real world"                                      │
│ - Runs Docker Desktop                                   │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │ BootCampProject (Dev Container)                   │  │
│  │ - "Your virtual workspace"                        │  │
│  │ - Runs inside Windows                             │  │
│  │ - Has Docker installed                            │  │
│  │                                                   │  │
│  │  ┌─────────────────────────────────────────────┐  │  │
│  │  │ Flask Container                             │  │  │
│  │  │ - "Your running application"                │  │  │
│  │  │ - Runs inside BootCampProject               │  │  │
│  │  │ - Created by docker build/run               │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  │                                                   │  │
│  │  You could create MORE containers here:           │  │
│  │  ┌───────────────┐ ┌───────────────┐             │  │
│  │  │ Database      │ │ Frontend      │             │  │
│  │  │ Container     │ │ Container     │             │  │
│  │  └───────────────┘ └───────────────┘             │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

From project root (workspaces/AWS-CloudCamp) go to frontend-react-js folder and run npm -i to install npm.
Inside we can also run manually the commands 
```
docker build -t frontend-react-js ./frontend-react-js
```
to create an image, based on the Docker file created inside the folder.
```
#Dockerfile
FROM node:16.18
# Set default port environment variable
ENV PORT=3000
# Create working directory inside the container
COPY . /frontend-react-js
# Set working directory (inside the container)
WORKDIR /frontend-react-js
# Install dependencies (inside the container)
RUN npm install
# Expose the port (inside the container)
EXPOSE ${PORT}
# Command to run the application (inside the container)
CMD ["npm", "start"]
```
and using 
```
docker run -p 3000:3000 -d frontend-react-js 
```
will start a container based on the image created previously.

Also can be done for backend-flask application
Build a image from
```
#Docker file
#create a docker image using python 3.10 slim buster as the base image
#create another container only for the backend-flask application
FROM python:3.10-slim-buster

# Set working directory (inside the container)
# make a new folder in the container called backend-flask
WORKDIR /backend-flask

# Install dependencies (from outside the container to inside the container)
COPY requirements.txt requirements.txt

# Install required packages(inside the container)
RUN pip3 install -r requirements.txt

# Copy all files from the current directory (outside the container) to the working directory (inside the container)
# first . means everything from the current directory outside the container (/backend-flask)
# second . means copy to the working directory inside the container (/backend-flask)
COPY . .

# set environment variables inside the container and remain set when the container is running
ENV FLASK_ENV=development

EXPOSE ${PORT}

# Command to run the application (inside the container)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]
```
using 
```
build -t backend-flask ./backend-flask
```
and run the container from that image using:
```
docker run --rm -p 4567:4567 -it -e FRONTEND_URL="*" -e BACKEND_URL="*" backend-flask
```
and access the backend-page using http://localhost:4567/api/activities/home

Using docker-compose.yml 
```
version: "3.8"
services:
  backend-flask:
    environment:
      #frontend url is where the frontend is hosted
      FRONTEND_URL: "http://localhost:3000"
      #backend url is where the backend is hosted
      BACKEND_URL: "http://localhost:4567"
    #this is the path to the Dockerfile  
    build: ./backend-flask
    # the ports mapping from host to container allowing access to the backend from outside the container
    ports:
      - "4567:4567"
    # this is the path mapping from host to container, more precisely from local machine to the docker container
    volumes:
      - ./backend-flask:/backend-flask
    # this is the network the container will use to communicate with other containers 
    networks:
      - internal-network
  #this is the frontend service using react js
  frontend-react-js:
    #the environment variable to point to the backend service
    environment:
      REACT_APP_BACKEND_URL: "http://localhost:4567"
    #this is the path to the Dockerfile holding the instructions to build the image
    build: ./frontend-react-js
    # the ports mapping from host to container so we can access the frontend from outside the container
    ports:
      - "3000:3000"
    # this is the path mapping from host to container, more precisely from local machine to the docker container
    volumes:
      - ./frontend-react-js:/frontend-react-js
    # this is the network the container will use to communicate with other containers
    networks:
      - internal-network

#the name flag is a hack to change the default prepend folder name when outpulling image names
# this is useful for CI/CD pipelines
networks:
  internal-network:
    driver: bridge
    name: cruddur
```
we can run multiple containers at the same time.
Then run command docker-compose up, and access [port](http://localhost:3000/) where will have this, which show that backend is connected to frontend <img width="1474" height="839" alt="image" src="https://github.com/user-attachments/assets/a4ac1183-7c65-4a78-b45d-fe1383238ba4" />

# Home Challenges

## Challenge1 Push and tag a image to DockerHub (they have a free tier)
- 1.First create an docker hub account and a repository. My repository is "testrepository"
- 2.run command ```docker images ``` to see what images you have. I locally I have image for the whole project, which I use to create a container where I run other containers.
- 3. Tag the image like this ``` docker tag imageID AccountName/Respository:Tagname ```, for me would be something like this ```docker tag 91f8e1f54a8a alexcruceru/testrepository:vsc-aws-cloudcamp``` where 91f8e1f54a8a is the ID of the image, alexcruceru is the name of the account, testrepository the repository name, and vsc-aws-cloudcamp the tag I choose, which is the same as the image name, but can be anything else.
- 4. push the image with the following command ``` docker push AccountName/Repository:Tagname ``` so for me would be ```docker push alexcruceru/testrepository:vsc-aws-cloudcamp```
- 5. As I'm doing my project inside an container, the one I talked about previously "vsc-aws-cloudcamp" to push the frontend and backend images, I had to first start the container, then docker-compose up, and again doing same steps of ```docker images``` and then tagging and pushing the images

## Use multi-stage building for a Dockerfile build

- For this, dockerfiles need to be modified as such:
  ```
  # STAGE 1
  FROM python:3.10-slim-buster AS builder

	# Set working directory (inside the container)
	# make a new folder in the container called backend-flask
	WORKDIR /backend-flask

	# Install dependencies (from outside the container to inside the container)
	COPY requirements.txt requirements.txt


	# Install required packages(inside the container)
	RUN pip install --no-cache-dir -r requirements.txt

  	# STAGE 2
  FROM python:3.10-slim-buster

	# Set working directory (inside the container)
	# make a new folder in the container called backend-flask
	WORKDIR /backend-flask
	
	# Copy installed Python packages from builder stage
	COPY --from=builder /usr/local /usr/local
	
	# Copy all files from the current directory (outside the container) to the working directory (inside the container)
	# first . means everything from the current directory outside the container (/backend-flask)
	# second . means copy to the working directory inside the container (/backend-flask)
	COPY . .
	
	# set environment variables inside the container and remain set when the container is running
	ENV FLASK_ENV=development
	
	EXPOSE ${PORT}
	
	# Command to run the application (inside the container)
	# python3 -m flask run --host=0.0.0.0 --port=4567
	CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]

  ```
  frontend Docker:
  ```
  # 1st stage: 
	FROM node:16.18 AS builder
	
	# Set working directory (inside the container)
	WORKDIR /frontend-react-js
	# Create working directory inside the container
	COPY . /frontend-react-js
	# Install dependencies (inside the container)
	RUN npm install
	
	# 2nd stage: build the application
	FROM node:16.18-slim
	# Set default port environment variable
	ENV PORT=3000
	
	# Set working directory (inside the container)
	WORKDIR /frontend-react-js
	
	# Copy node_modules from builder stage
	COPY --from=builder /frontend-react-js/node_modules ./node_modules
	
	#   Copy application source code from builder stage
	COPY . .
	
	# Expose the port (inside the container)
	EXPOSE ${PORT}
	# Command to run the application (inside the container)
	CMD ["npm", "start"]
  ```
Stage 1 is for bulding:

- installing dependencies
- compiling code
- building artifacts
- doing heavy or dirty work
- Things that are needed to build, but not needed to run.
Stage 2 is for runtime:
- running the application
- being small, clean, minimal
- containing only what is required at runtime
So only stage 2 exists after building the image, which results in a smaller image.

### Health checks added to multistage version
- Created a new breanch "docker_multistage"
- Had some trouble. As frontend has dependency of backend to be healthy, found out that my backend was unhealthy. The cause was found using ``` docker insepct *containerid* ``` and then searching for health, where shows health logs, and there says that curl was not found So I had to install it because my test use the curl command
- Initialy installed in first stage of the docker file and this would still  show the container as unhealthy, fixed by moving to second stage. Also used different images as base, because if used slim in first stage and simple in second, would have some debian issue.
- For frontend added initialy healthchecks and also installed the curl, but seems to be issue with the slim image because slim images remove a lot of Debian infrastructure. Removed that check because React dev server already crashes if it can’t start, Docker already knows if the container is running, This healthcheck adds almost no value.
- Also found that if checks are going ok, when using docker ps, it will shows the status as healthy, but if a check fail, will show it as health:starting untill finishes all checks.

## Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
- A good practice I saw was to use .slim or .alpine variant of images for reduced size in final image. Sometime could be compatibility problems if in builder stage you use a example.3.1 and in second stage example.3.1.slime.(Binary incompatibility: if your builder stage installs something that depends on glibc version, OS libraries, or compiled C extensions, the runtime image must be compatible.)
- In docker file specified to copy only the dependencies. If nothing changed in a step, Docker would reuse what was built before. Should be added before installing the dependencies(before RUN command)
- Added .dockerignore. When you run docker build, Docker sends the entire folder (build context) to the Docker daemon. .dockerignore tells Docker which files/folders to exclude from this context. For example node_modules, from frontend. Those modules would be installed anyway by npm install command, so is no need to copy them, also .vscode are no needed by the image, .git files etc, no needed.


# Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes.
- Created EC2 instance, with default values, linx OS, generated keys for SSH login, at security group, edited and added type SSH, and 2 security groups for frontend and backend ports.
- Launch the EC2 instance. Where I have the .pem file (the keys) right click and choose git bash. Run the following commands ``` chmod 400 "Ec2Keys.pem"  ``` (so key won't be public viewable) and ``` ssh -i "Ec2Keys.pem" ec2-user@ec2-3-236-252-21.compute-1.amazonaws.com ```.
- Install docker: 1) ``` sudo yum update -y ```(ensure your package database is up to date) 2) ``` sudo yum install docker -y ``` 3) ``` sudo systemctl start docker ```(By default, the service is installed but not running)  4) ``` sudo systemctl enable docker ``` (This ensures that if your EC2 instance restarts, Docker will start automatically.) If we try now to run "docker ps" command, will get an error because we don't have permission, so next we do 5)```sudo usermod -aG docker ec2-user``` (to apply docker commands withous using sudo) 6) ``` newgrp docker ``` (to refresh session so previous command takes effect)
- Docker login: 1) ``` docker login ``` 2) Generate from docker hub a new tocken 3) in the prompt that appears enter the name and tocken generated, will show login succeeded.
- Pull the images and test ``` docker pull alexcruceru/testrepository:frontend-react-js-image-multistage ``` test by running command ``` docker run -p 3000:3000 -d alexcruceru/testrepository:frontend-react-js-image-multistage ``` test in browser "http://3.236.252.21:3000/". Also for backend copy pull the image, start the container with ``` docker run --rm -p 4567:4567 -it   -e FRONTEND_URL="*"   -e BACKEND_URL="*"   alexcruceru/testrepository:backend-flask-image-multistage ``` and test with link in browser ``` http://3.236.252.21:4567/api/activities/home ```.
- Tested also by pulling the project from github.
