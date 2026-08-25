# CI/CD Pipeline with GitHub Actions & Docker

A simple Flask web application demonstrating a complete CI/CD pipeline: every push to `main` is automatically tested, containerized, and published to Docker Hub using GitHub Actions.

## Overview

This project implements a Continuous Integration / Continuous Delivery workflow using free, widely-used DevOps tools — no cloud account required. The pipeline runs automated tests, builds a Docker image, and pushes it to a container registry, ensuring that only verified, working code ever gets published as a deployable image.

## Tech Stack

- **Application:** Python, Flask
- **Testing:** Pytest
- **Containerization:** Docker
- **CI/CD:** GitHub Actions
- **Container Registry:** Docker Hub

## Project Structure

```
cicd-demo-app/
├── app.py                          # Flask application
├── test_app.py                     # Automated tests
├── requirements.txt                # Python dependencies
├── Dockerfile                      # Container build instructions
├── docker-compose.yml              # Local multi-service run config
└── .github/
    └── workflows/
        └── docker-image.yml        # CI/CD pipeline definition
```

## How the Pipeline Works

On every push to the `main` branch, GitHub Actions automatically:

1. **Checks out the code**
2. **Sets up Python** and installs dependencies from `requirements.txt`
3. **Runs automated tests** with `pytest` — if any test fails, the pipeline stops here and no image is built or published
4. **Logs in to Docker Hub** using encrypted repository secrets
5. **Builds the Docker image** from the `Dockerfile`
6. **Pushes the image** to Docker Hub, tagged `latest`

This "fail fast" ordering ensures a broken build can never reach the container registry.

## Running Locally

**Clone the repo:**
```bash
git clone https://github.com/shiva70131/cicd-demo-app.git
cd cicd-demo-app
```

**Run with Docker Compose:**
```bash
docker-compose up --build
```

**Or run the published image directly from Docker Hub:**
```bash
docker pull shiva70131/my-web-app:latest
docker run -p 5000:5000 shiva70131/my-web-app:latest
```

Then open [http://localhost:5000](http://localhost:5000) in your browser.

## Running Tests Locally

```bash
pip install -r requirements.txt
pytest
```

## CI/CD Secrets Configuration

The workflow expects the following repository secrets to be configured under **Settings → Secrets and variables → Actions**:

| Secret Name | Description |
|---|---|
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub access token (Read & Write scope) — generated under Docker Hub → Account Settings → Security |

## Key Learnings

- **Layer caching:** copying `requirements.txt` before the rest of the source code lets Docker reuse cached dependency layers, speeding up rebuilds when only application code changes.
- **Secrets must match exactly:** GitHub Actions silently substitutes empty values for secret names that don't exist, rather than erroring — an important debugging lesson when a build fails with vague authentication errors.
- **Token scope matters:** a Docker Hub access token must be generated with **Read & Write** permissions to allow pushing images; a Read-only token fails at the push step even after successful authentication.

## Live Image

Published image available at: [hub.docker.com/r/shiva70131/my-web-app](https://hub.docker.com/r/shiva70131/my-web-app)