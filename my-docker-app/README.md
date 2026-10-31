# my-docker-app

A small Python application packaged as a Docker image. This activity walks through building the image, running it locally, publishing it to Docker Hub, and pulling and running the published image.

## Objectives

- Write a simple Python application.
- Package it with Docker using a small Python base image.
- Build and test the image locally.
- Tag and publish the image to a Docker Hub repository.
- Pull the published image and run it.

## Technologies

- Python 3.12
- Docker
- Docker Hub
- Ubuntu (commands below work in the VS Code integrated terminal)

## Project structure

```text
my-docker-app/
├── app.py
├── Dockerfile
├── .dockerignore
└── README.md
```

## Application

`app.py` prints `Hello from Docker!`, a welcome message, the application name, and a confirmation that it is running in a container.

## Dockerfile explained

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

- `FROM python:3.12-slim` selects the official Python 3.12 slim base image. The slim variant avoids many tools that are not needed for this small application.
- `WORKDIR /app` sets the working directory inside the image. Docker creates it if it does not exist, and subsequent commands use it as their current directory.
- `COPY app.py .` copies only the application source into the image. Copying only what the app needs keeps the image and build context lean.
- `CMD ["python", "app.py"]` defines the default process when a container starts. The JSON/exec form runs Python directly without an extra shell process.

The `.dockerignore` file prevents Python cache files, local virtual environments, Git metadata, environment files, and editor/OS files from being sent as build context.

## Build the image locally

In the VS Code terminal, change to this project directory:

```bash
cd /var/www/html/DockerImage/my-docker-app
```

Build and tag the local image:

```bash
docker build -t my-docker-app:1.0 .
```

`docker build` builds an image from the Dockerfile; `-t my-docker-app:1.0` gives it the local name `my-docker-app` and version tag `1.0`; `.` sends the current directory as the build context.

Check that it exists:

```bash
docker images
```

Look for `my-docker-app` with tag `1.0` in the output.

## Run and test locally

```bash
docker run --rm my-docker-app:1.0
```

`docker run` creates and starts a container from the image. `--rm` removes that stopped container automatically, while preserving the image.

Expected output:

```text
Hello from Docker!
Welcome to my Docker application.
Application: my-docker-app
The application is running inside a Docker container.
```

An **image** is the packaged, read-only template containing the application and its runtime. A **container** is a running (or stopped) instance created from an image.

## Create a Docker Hub repository

1. Sign up or sign in at [Docker Hub](https://hub.docker.com/).
2. Create a repository named `my-docker-app`. Choose **Public** if you want anyone to be able to pull it.
3. Use your actual Docker Hub username in place of `YOUR_DOCKERHUB_USERNAME` in the commands below.

## Authenticate to Docker Hub

Before running an authentication command, make sure you have your Docker Hub account ready. In the VS Code terminal, use:

```bash
docker login
```

Follow the interactive prompts or browser-based sign-in. If Docker requests a password, use a Docker Hub access token if your account requires or prefers one. Never put your password or token in the project files, source control, or command-line arguments. Docker stores login credentials in its configured credential store (or Docker configuration).

## Tag and push the image

Tagging adds the Docker Hub namespace and repository name to the local image so Docker knows where to publish it:

```bash
docker tag my-docker-app:1.0 YOUR_DOCKERHUB_USERNAME/my-docker-app:1.0
```

Replace the placeholder with your Docker Hub username (without angle brackets), for example the command's image reference should follow `username/my-docker-app:1.0`.

Push it:

```bash
docker push YOUR_DOCKERHUB_USERNAME/my-docker-app:1.0
```

Replace the same placeholder with your username. A successful push shows the uploaded layers and a digest. You can also open the `my-docker-app` repository on Docker Hub and check that tag `1.0` appears.

## Pull and run the published image

Pull the image (replace the username placeholder first):

```bash
docker pull YOUR_DOCKERHUB_USERNAME/my-docker-app:1.0
```

Run the pulled image:

```bash
docker run --rm YOUR_DOCKERHUB_USERNAME/my-docker-app:1.0
```

The output should match the local test and begin with `Hello from Docker!`. A public image can be pulled without signing in; a private repository requires authentication.

## Expected results

- `docker images` lists `my-docker-app:1.0`.
- Running the local image prints the four application messages and exits successfully.
- Docker Hub shows the pushed `1.0` tag after a successful push.
- Pulling and running the Hub image prints the same messages.
