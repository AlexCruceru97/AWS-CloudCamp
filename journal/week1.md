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
