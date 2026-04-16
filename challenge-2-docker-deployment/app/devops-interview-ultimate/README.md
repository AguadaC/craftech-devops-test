# DevOps Interview Ultimate (Django + React)

This repository contains:

- Backend: [Django + Django REST Framework](backend/README.md)
- Frontend: [React (Create React App)](frontend/README.md)
- DB: PostgreSQL

## Relationship between frontend and backend

The frontend calls the backend via HTTP using `axios`.

The API base URL is hardcoded in:

- `frontend/src/config/constant.js`
- `API_SERVER = "http://localhost:8000/api/"`

Because of that, the browser expects the backend to be reachable from your
machine at:
`http://localhost:8000`.

## Challenge 2: bring everything up with Docker Compose

Option A runs the frontend with `npm start` (development mode) and the backend
with Django `runserver`.

### Requirements

- Docker
- Docker Compose plugin (`docker compose`)

### Bring up the stack

From the repository root:

```bash
docker compose up --build
```

Note: the backend waits for Postgres to be "healthy" via healthcheck.

Services:

- Frontend: http://localhost:3000
- Backend: http://localhost:8000
- Postgres: localhost:5432

## Note about migrations

The backend container runs migrations on startup.

Note: the backend `entrypoint.sh` runs `flush` on each startup and loads
initial data. If you want to persist the DB, comment out the `flush` line.

## Environment variables

Backend configuration in Docker is defined in:

- `backend/.env.backend`

Expected variables in `backend/.env.backend`:

- `DATABASE`: DB type (in this compose: `postgres`)
- `DEBUG`: 1/0 to enable debug mode
- `DJANGO_ALLOWED_HOSTS`: allowed hosts (e.g. `*` for local)
- `SQL_ENGINE`: Django engine (e.g. `django.db.backends.postgresql`)
- `SQL_DATABASE`: database name
- `SQL_USER`: database user
- `SQL_PASSWORD`: database password
- `SQL_HOST`: database host (in compose: `db`)
- `SQL_PORT`: database port (default: 5432)

Postgres configuration is defined in:

- `backend/.env.postgres`

Expected variables in `backend/.env.postgres`:

- `POSTGRES_DB`: database name
- `POSTGRES_USER`: user
- `POSTGRES_PASSWORD`: password

Note: `docker-compose.yml` already references these files. You do not need to
export variables in your shell to run locally.

## Cloud deployment (AWS/GCP)

On a VM (EC2 or Compute Engine) you can:

- Install Docker and Docker Compose
- Clone the repo
- Run `docker compose up -d --build`

For a more serious deployment, Option B is recommended for the frontend.

[go to back](../../README.md)
