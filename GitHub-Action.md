# GitHub Action CI/CD

This document describes a basic GitHub Actions CI/CD workflow for building, testing, and deploying a repository.

## Overview

- GitHub Actions runs workflows defined in `.github/workflows/*.yml`.
- A workflow can use jobs, steps, and actions to build, test, and deploy code automatically.
- Common triggers: `push`, `pull_request`, `schedule`, `workflow_dispatch`.

## Example Workflow

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

```

## Key concepts

- `jobs`: separate units of work that run in parallel by default.
- `runs-on`: the runner environment, such as `ubuntu-latest`.
- `steps`: individual commands or actions executed in a job.
- `uses`: an action from the marketplace or repository.
- `run`: shell commands executed on the runner.
- `needs`: defines job dependencies.
- `if`: conditional execution.

## Best practices

- Keep workflows focused and readable.
- Cache dependencies when possible.
- Use secrets for credentials (`secrets.MY_SECRET`).
- Require pull request checks before merging.

## Workflow file location

Place workflow files in:

```
.github/workflows/<workflow-name>.yml
```
