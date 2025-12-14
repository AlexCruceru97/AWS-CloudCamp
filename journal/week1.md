# Week 1 — App Containerization

## Containterization
Locally using docker I create an container called "BootCampProject"(name can be seen in devcontainer.json). Which will now will become my workspace/windows. Here I can launch other containers, for example container for flask application, databases etc.
Using 
``` "features": {
		"ghcr.io/devcontainers/features/docker-in-docker:2":{} 
	},
```
allows me to use docker commands inside the container BootCampProject, how I am doing inside Windows.
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
