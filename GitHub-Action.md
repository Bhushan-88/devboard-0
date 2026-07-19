# GitHub Actions CI Pipeline

This document describes the CI pipeline for the DevBoard project using GitHub Actions.

## Overview
The workflow runs automatically on every push to the advanced branch and performs the following tasks:

- Frontend linting and testing
- Frontend Docker image build and push to Docker Hub
- Backend linting, formatting, and testing
- Backend Docker image build and push to Docker Hub

## Trigger
The workflow is triggered on:

- Push to the advanced branch

## Jobs

### 1. Frontend-lint
This job checks the frontend code by:

- Checking out the repository
- Setting up Node.js 20
- Installing dependencies with npm ci --legacy-peer-deps
- Running ESLint
- Running frontend tests

### 2. Frontend-build-and-push
This job runs after the frontend lint job succeeds and:

- Logs in to Docker Hub
- Builds the frontend Docker image
- Pushes the image to Docker Hub using the tag:
  - username/devboard-fe-advanced:latest

### 3. Backend-lint
This job validates the backend code by:

- Checking out the repository
- Setting up Go 1.23
- Running gofmt
- Running go vet
- Running Go tests

### 4. Backend-build-and-push
This job runs after the backend lint job succeeds and:

- Logs in to Docker Hub
- Builds the backend Docker image
- Pushes the image to Docker Hub using the tag:
  - username/devboard-be-advanced:latest

## Required GitHub Configuration
To make the workflow work, configure the following in GitHub repository settings:

- Secret: DOCKERHUB_TOKEN
- Variable: DOCKERHUB_USERNAME

## Workflow File
The workflow is defined in:

- .github/workflows/CI-pipeline.yml

## Summary
This pipeline helps ensure code quality and automatically publishes updated frontend and backend images whenever changes are pushed to the advanced branch.


# GitHub Actions CD Pipeline

This document describes the CD pipeline for the DevBoard project using GitHub Actions.

## Overview
The workflow runs automatically after the CI pipeline completes successfully on the advanced branch and performs the following tasks:

- Checkout the repository code
- Copy the environment file for deployment
- Log in to Docker Hub
- Pull the latest Docker images
- Start the containers using Docker Compose

## Trigger
The workflow is triggered on:

- Completion of the CI-pipeline workflow
- Push or merge on the advanced branch

## Job

### 1. Deploy
This job runs on a self-hosted runner and:

- Checks out the repository
- Copies .env.example to .env
- Logs in to Docker Hub using GitHub secrets and variables
- Pulls the latest images from Docker Compose
- Starts the containers in detached mode

## Required GitHub Configuration
To make the workflow work, configure the following in GitHub repository settings:

- Secret: DOCKERHUB_TOKEN
- Variable: DOCKERHUB_USERNAME

## Workflow File
The workflow is defined in:

- .github/workflows/CD-pipeline.yml

## Self-Hosted Runner Setup
Use an EC2 instance as a self-hosted GitHub Actions runner.

### Runner setup steps

```bash
Use EC2 instance as a Self hosted Runner
Ceate AWS EC2 Instance

## Runner set up
Goto github action on Github Repository Advanced =>Setting ->Actions ->Runners ->New self hosted runner-> select Linux

mkdir actions-runner && cd actions-runner

curl -o actions-runner-linux-x64-2.335.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.335.1/actions-runner-linux-x64-2.335.1.tar.gz

echo "4ef2f25285f0ae4477f1fe1e346db76d2f3ebf03824e2ddd1973a2819bf6c8cf  actions-runner-linux-x64-2.335.1.tar.gz" | shasum -a 256 -c

tar xzf ./actions-runner-linux-x64-2.335.1.tar.gz


ubuntu@ip-172-31-73-104:~/actions-runner$ ./config.sh --url https://github.com/Bhushan-88/devboard-0 --token A6FFDMO3E3SUFKDXZI5IIF3KLIDRK 

--------------------------------------------------------------------------------
|        ____ _ _   _   _       _          _        _   _                      |
|       / ___(_) |_| | | |_   _| |__      / \   ___| |_(_) ___  _ __  ___      |
|      | |  _| | __| |_| | | | | '_ \    / _ \ / __| __| |/ _ \| '_ \/ __|     |
|      | |_| | | |_|  _  | |_| | |_) |  / ___ \ (__| |_| | (_) | | | \__ \     |
|       \____|_|\__|_| |_|\__,_|_.__/  /_/   \_\___|\__|_|\___/|_| |_|___/     |
|                                                                              |
|                       Self-hosted runner registration                        |
|                                                                              |
--------------------------------------------------------------------------------

# Authentication


√ Connected to GitHub

# Runner Registration

Enter the name of the runner group to add this runner to: [press Enter for Default]

Enter the name of runner: [press Enter for ip-172-31-73-104] self-hosted

This runner will have the following labels: 'self-hosted', 'Linux', 'X64'
Enter any additional labels (ex. label-1,label-2): [press Enter to skip]

√ Runner successfully added

# Runner settings

Enter name of work folder: [press Enter for _work] devboard-0-work

√ Settings Saved.

ubuntu@ip-172-31-73-104:~/actions-runner$ ./run.sh

√ Connected to GitHub

Current runner version: '2.335.1'
2026-07-17 09:48:40Z: Listening for Jobs

## Now check runner on github status will idel

```

### Install Docker on the runner
```bash
# install docker and docker compose on self-hosted runner 
sudo apt-get update && sudo apt-get install docker.io docker-compose-v2 -y 

sudo usermod -aG docker $USER
newgrp docker

```

## Summary
This CD pipeline deploys the latest application containers automatically after a successful CI run on the advanced branch.