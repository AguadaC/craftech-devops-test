# Challenge 3 - CI (Nginx)

## Goal

- Dockerize an Nginx container serving a custom `index.html`.
- Implement CI with GitHub Actions so that each change to `index.html`
  triggers a new image build, smoke test, and push to a registry.

## Files

- [`Dockerfile`](Dockerfile)
- [`index.html`](index.html)
- [`docker-compose.yml`](docker-compose.yml) (only for local testing)
- Workflow: [`.github/workflows/challenge-3-ci.yml`](../.github/workflows/challenge-3-ci.yml)

## How GitHub Actions works

The workflow runs on `push` only if these paths change:

- `challenge-3-cicd-nginx/index.html`
- `challenge-3-cicd-nginx/Dockerfile`

Then:

- Logs in to GHCR (GitHub Container Registry).
- Builds the image.
- Runs a smoke test by starting a container and checking that it responds with HTTP 200 and the `Server: nginx` header.
- Pushes the image to GHCR with the `latest` tag and a short commit SHA tag.

The image is published at:

- `ghcr.io/<owner>/<repo>/challenge-3-nginx:latest`

And also:

- `ghcr.io/<owner>/<repo>/challenge-3-nginx:sha-<7-char-sha>`

## How to verify it ran

On GitHub:

- Tab `Actions` -> workflow `Challenge 3 - CI (Nginx)`

You should see the job `build-and-push` in green.

## Pull the generated image (GHCR)

The image is published to GHCR as:

- `ghcr.io/<owner>/<repo>/challenge-3-nginx:latest`

To pull it:

```bash
docker pull ghcr.io/<owner>/<repo>/challenge-3-nginx:latest
```

This repository is public, so pulling should work without authentication.

Note: GHCR image names must be lowercase. If your GitHub owner/repo contains uppercase characters, use the lowercase form in the image name.

Then you can run the image directly:

```bash
docker run --rm -p 8080:80 ghcr.io/<owner>/<repo>/challenge-3-nginx:latest
```

Or using a specific build:

```bash
docker run --rm -p 8080:80 ghcr.io/<owner>/<repo>/challenge-3-nginx:sha-<7-char-sha>
```

Open:

- `http://localhost:8080`

## Local test

From `challenge-3-cicd-nginx/`:

```bash
export GITHUB_REPOSITORY_LOWER=<owner>/<repo>
docker compose pull
docker compose up
```

Open:

- `http://localhost:8080`

[go to back](../README.md)
