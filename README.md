# README APCV 405 Class Project

## Project Description

An ASP.NET Core web application, containerized with Docker and built, tested, and
deployed through an automated Jenkins pipeline. The application listens on port 8080
inside its container, is published on host port 9090, and exposes a health endpoint
at `/health`.

## Build Instructions

Prerequisites: .NET 8 SDK, Docker Desktop, Git.

```bash
git clone https://github.com/goadzirra/apcv_405_app.git
cd apcv_405_app
dotnet test apcv_405_app.sln
docker build -t ccgoad/apcv_405_app:latest .
```

## Deployment Steps

```bash
docker pull ccgoad/apcv_405_app:latest
docker run -d --name apcv_405_app -p 9090:8080 ccgoad/apcv_405_app:latest
```

The application is then reachable at http://localhost:9090, with the health endpoint
at http://localhost:9090/health.

## CI/CD Pipeline

The pipeline is defined in `Jenkinsfile` and executed by Jenkins. Six stages:

1. **Checkout** - clones the repository from GitHub
2. **Test** - runs the test suite with `dotnet test`
3. **Build Docker Image** - builds the container image from the Dockerfile
4. **Push to DockerHub** - authenticates with stored Jenkins credentials and publishes
5. **Deploy Locally** - runs the container on host port 9090
6. **Health Check** - confirms the deployed application responds at `/health`

A failure in any stage halts the pipeline, so a broken test or a failed build never
reaches deployment.

### Container Registry

Published image: https://hub.docker.com/r/ccgoad/apcv_405_app
