# GitHub Actions workflows

This directory contains the workflows used to validate, publish, and deploy
DevBoard. The main CI workflow runs on pushes to `feat/matrix-docker-build`;
update its branch filter in `CI-pipeline.yml` if the target branch changes.

## Workflows

### `CI-pipeline.yml` — CI, image publishing, and deployment

On a push to `feat/matrix-docker-build`, this workflow runs:

1. **Frontend checks** in `frontend/` using Node.js 20: installs dependencies
   with `npm ci --legacy-peer-deps`, runs `npm run lint`, and runs
   `npm run test`.
2. **Backend checks** in `backend/`: runs `go fmt ./...`, `go vet ./...`, and
   `go test ./...`. The Go version is read from `backend/go.mod`.
3. **Image publishing**, only after both check jobs succeed: builds and pushes
   the `frontend` and `backend` Docker images, tagged
   `<DOCKERHUB_USERNAME>/devboard-frontend:latest` and
   `<DOCKERHUB_USERNAME>/devboard-backend:latest`.
4. **Deployment**, only after checks and image publishing succeed: calls
   `CD-pipeline.yml`.

### `CD-pipeline.yml` — reusable deployment

This workflow is called by another workflow (`workflow_call`); it is not
configured as a standalone push or manual workflow. It runs on a self-hosted
runner, copies `.env.example` to `.env`, logs in to DockerHub, then runs
`docker compose pull` and `docker compose up -d`.

The runner must be configured with Docker and Docker Compose and have access to
the deployment environment and the repository's Compose configuration.

### `matrix.yml` — manually triggered Go checks

Run this workflow from the GitHub Actions tab with **Run workflow**. It checks
the backend with Go versions 1.22, 1.23, and 1.24, running `go fmt` and
`go vet` in `backend/`. It does not run Go tests or publish images.

## Repository configuration

Configure these repository-level settings before running CI:

| Name | Type | Used for |
| --- | --- | --- |
| `DOCKERHUB_USERNAME` | Actions variable | DockerHub login and image names |
| `DOCKERHUB_TOKEN` | Actions secret | DockerHub authentication |

The deployment job inherits the calling workflow's secrets. The self-hosted
runner used for deployment must be online and available to the repository.
