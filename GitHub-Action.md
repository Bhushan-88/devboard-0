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
