# Node.js CI/CD with GitHub Actions, Docker & GCP Compute Engine

[![CI/CD Workflow](https://github.com/arjunrvk2502/Node.js-CICD/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/arjunrvk2502/Node.js-CICD/actions/workflows/ci-cd.yml)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A comprehensive CI/CD pipeline for a Dockerized Node.js application that uses GitHub Actions for CI/CD and deploys containers to a Google Cloud Compute Engine VM.

## Table of Contents

- [About this project](#about-this-project)
- [How this project works](#how-this-project-works)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Pipeline Stages](#pipeline-stages)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## About this project

This repository demonstrates a complete, repeatable CI/CD workflow for a Node.js web application. The pipeline builds a Docker image, publishes it to a container registry, and deploys the container to a Google Cloud Compute Engine (GCE) VM via SSH — all orchestrated by GitHub Actions. The goal is to show a practical end-to-end flow from developer push to running service on a VM.

## How this project works

High-level flow (step-by-step):

1. Developer writes code and pushes to the repository (typically the `main` branch).
2. GitHub Actions detects the push (or PR) and starts the CI workflow:
   - Checkout repository
   - Set up Node.js environment
   - Install dependencies
   - Run tests and linters
3. If tests pass, the workflow builds a Docker image from the repository (Dockerfile).
4. The workflow authenticates to Docker Hub (or your chosen registry) and pushes the image (tagged, e.g., `:latest` or with commit SHA).
5. The workflow establishes an SSH connection to the GCE VM using a private key stored in GitHub Secrets.
6. On the VM, the workflow runs deployment commands:
   - Pull the latest image from Docker Hub
   - Stop and remove the previous container (if present)
   - Start a new container exposing the application port (e.g., 3000)
7. The application is now running on the VM and reachable via the VM external IP at the exposed port.

Secrets used by the workflow (store in GitHub repository secrets):
- DOCKER_USERNAME — Docker Hub username
- DOCKER_PASSWORD — Docker Hub password or access token
- GCP_HOST — External IP or hostname of the GCE VM
- GCP_USER — SSH username on the GCE VM
- GCP_SSH_KEY — Private SSH key used by GitHub Actions to connect to the VM

Security notes:
- Never commit keys or passwords to the repo. Use GitHub Secrets.
- For production, prefer managed services, secret stores, and service accounts with least privilege.

## Features

- Automated CI with GitHub Actions
- Dockerized Node.js app with reproducible runtime
- Automated deploy to GCE VM using SSH
- Simple and educational pipeline, easy to extend
- Clear separation of stages: test → build → publish → deploy

## Prerequisites

- Git
- Node.js (v18 recommended)
- Docker (for local testing/build)
- Google Cloud account with a Compute Engine VM (Docker installed)
- Docker Hub account (or another container registry)

## Installation

1. Clone the repository:
```bash
git clone https://github.com/arjunrvk2502/Node.js-CICD.git
cd Node.js-CICD
```

2. Install dependencies:
```bash
npm install
```

3. Copy environment examples (if provided):
```bash
cp .env.example .env
# Edit .env with your settings
```

## Configuration

- Add repository secrets (Settings → Secrets and variables → Actions):
  - DOCKER_USERNAME
  - DOCKER_PASSWORD
  - GCP_HOST
  - GCP_USER
  - GCP_SSH_KEY
- Ensure the GCE VM has Docker installed and the public part of the SSH key is added to VM metadata (or the user’s authorized_keys).
- Open the VM firewall for the application port (e.g., 3000) and SSH (port 22).

## Usage

Run locally:
```bash
npm start
# or
node index.js
```

Build and run the Docker image locally:
```bash
docker build -t cicd-test .
docker run -d --name demo -p 3000:3000 cicd-test
```

Trigger the pipeline by pushing to `main` or opening a pull request.

## Pipeline Stages

1. Test
   - Install dependencies
   - Run unit tests and linters
2. Build
   - Build Docker image from the repository
3. Publish
   - Authenticate to Docker Hub and push the image
4. Deploy
   - SSH to GCE VM and run deploy script (pull, stop old, run new container)

## Troubleshooting

- Permission denied with Docker: run `sudo usermod -aG docker $USER` and reconnect.
- SSH connection errors: confirm private key in secrets, public key in VM metadata, and that SSH port is open.
- Port already in use: `docker ps` → `docker stop <container>` → `docker rm <container>`.
- GitHub Actions fails to push to Docker Hub: verify `DOCKER_USERNAME` and `DOCKER_PASSWORD` secrets (use an access token for enhanced security).

## Contributing

Contributions welcome:
1. Fork the repository
2. Create a branch: `git checkout -b feature/your-change`
3. Commit changes and push
4. Open a Pull Request

Please ensure tests pass and CI is green before merging.

## License

This project is licensed under the MIT License — see the LICENSE file for details.

---

**Last Updated:** 2026-01-13

For help, open an issue or message the repository maintainer.
