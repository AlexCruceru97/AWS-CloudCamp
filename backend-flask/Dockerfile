#create a docker image using python 3.10 slim buster as the base image
#create another container only for the backend-flask application
FROM python:3.10-slim AS builder

# Set working directory (inside the container)
# make a new folder in the container called backend-flask
WORKDIR /backend-flask

# Install dependencies (from outside the container to inside the container)
COPY requirements.txt requirements.txt


# Install required packages(inside the container)
RUN pip install --no-cache-dir -r requirements.txt
# Install curl for healthcheck

# Copy all files from the current directory (outside the container) to the working directory (inside the container)
# first . means everything from the current directory outside the container (/backend-flask)
# second . means copy to the working directory inside the container (/backend-flask)
COPY . .

# 2nd stage: create the final image
FROM python:3.10-slim

# Set working directory (inside the container)
# make a new folder in the container called backend-flask
WORKDIR /backend-flask
ENV PORT=4567
#install runtime-only OS dependencies (cur for health check)
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
# copy from builder
COPY --from=builder /backend-flask /backend-flask
# Copy installed Python packages from builder stage
COPY --from=builder /usr/local /usr/local

# set environment variables inside the container and remain set when the container is running
ENV FLASK_ENV=development

EXPOSE ${PORT}

# Command to run the application (inside the container)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]