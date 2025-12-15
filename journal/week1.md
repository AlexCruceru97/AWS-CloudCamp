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
