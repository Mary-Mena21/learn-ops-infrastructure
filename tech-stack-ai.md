# Tech Stack (AI)

## 1. Run Questions

### 1a. Config Files

| Config File | Location | Config Value | What it's for | How it's used |
|---|---|---|---|---|
| .env | learn-ops-infrastructure/ | POSTGRES_DB | Name of the PostgreSQL database | Set at first boot; referenced by the API and postgres_exporter to target the correct database |
| .env | learn-ops-infrastructure/ | POSTGRES_USER | Database login username | Created by PostgreSQL at startup; used to authenticate all service connections |
| .env | learn-ops-infrastructure/ | POSTGRES_PASSWORD | Database login password | Used alongside POSTGRES_USER to authenticate connections from the API and exporter |
| .env | learn-ops-infrastructure/ | DATA_SOURCE_NAME | Full connection string for the metrics exporter | Read by postgres_exporter to know which PostgreSQL instance to connect to and scrape |
| docker-compose.yml | learn-ops-infrastructure/ | image | Docker image and version per service | Pins a specific version (e.g. postgres:16) to ensure consistent software across machines |
| docker-compose.yml | learn-ops-infrastructure/ | depends_on | Startup ordering between services | Prevents a service from starting before its dependency is ready or healthy |
| docker-compose.yml | learn-ops-infrastructure/ | ports | Host-to-container port mappings | Exposes services on localhost so a developer can reach them from outside Docker |
| docker-compose.yml | learn-ops-infrastructure/ | volumes | Storage mounts into containers | Persists database data across restarts and syncs local source code into containers |
| prometheus.yml | learn-ops-infrastructure/ | scrape_interval | How often Prometheus polls each target | Fires an HTTP request to every scrape target every 15 seconds |
| prometheus.yml | learn-ops-infrastructure/ | metrics_path | URL path Prometheus hits on each target | Set to /metrics/metrics to match the path that django-prometheus exposes |
| prometheus.yml | learn-ops-infrastructure/ | targets | Addresses of services to monitor | Points Prometheus at api:8000 for Django metrics and postgres_exporter:9187 for database metrics |

### 1b. How to Start It

**make setup**
What it does: Runs `scripts/setup.sh`, an interactive first-time wizard that clones the sibling repos (`learn-ops-api`, `learn-ops-client`, `service-monarch`), collects secrets (GitHub PAT, Slack token, Django secret key), writes all `.env` files from templates, seeds the instructor database fixture, and optionally starts the full stack.
When to use it: The very first time you set up the project on a new machine. It is idempotent — re-running it skips steps that are already complete.

**make up**
What it does: Runs `docker compose up --build -d` for all services — database, api, client, prometheus, grafana, and postgres_exporter — rebuilding images before starting, in detached mode.
When to use it: When you want the complete stack running, including the monitoring layer (Prometheus and Grafana).

**make up-api**
What it does: Runs `docker compose up --build -d api`, rebuilding and starting only the api service in detached mode.
When to use it: When you are working on backend code only and do not need the React client or the monitoring stack.

**make up-client-api**
What it does: Runs `docker compose up --build -d api client`, rebuilding and starting only the api and client services in detached mode.
When to use it: When you are working on the frontend and need the API responding, but do not need Prometheus or Grafana running.

**make restart**
What it does: Runs `docker compose down` followed by `docker compose up --build -d` — a full stop, rebuild, and restart of all services.
When to use it: When a configuration or Dockerfile change is not being picked up by a running container and you need a clean rebuild.

The three `up*` targets differ only in scope: `make up` starts every service including the monitoring stack, `make up-client-api` narrows that to the application tier (api + client), and `make up-api` narrows it further to the backend alone. All three pass `--build` so images are always rebuilt before starting. `make restart` does the same full rebuild as `make up` but first tears down whatever is already running. `make setup`, by contrast, is not a Docker Compose command at all — it is a one-time machine provisioning wizard that runs before any containers exist, installing prerequisites, cloning repos, and writing the `.env` files that the `up*` targets depend on.

### 1c. Where to Access It

| Service | Port | URL |
|---|---|---|
| database (Postgres) | 5432 | postgresql://localhost:5432 |
| api (Django) | 8000 | http://localhost:8000 |
| api (debugger) | 5678 | localhost:5678 (debugpy attach — not a browser URL) |
| client (React) | 3000 | http://localhost:3000 |
| prometheus | 9090 | http://localhost:9090 |
| grafana | 3001 | http://localhost:3001 |
| postgres_exporter | 9187 | http://localhost:9187/metrics |

### 1d. Service Dependencies

| Service | Depends On | Why |
|---|---|---|
| api | database | Django runs migrations and opens a connection pool at startup; if Postgres is not healthy the API process crashes immediately |
| prometheus | api | Prometheus's django scrape job targets api:8000/metrics/metrics; if the API is down that job returns connection errors and no application metrics are collected |
| prometheus | postgres_exporter | Prometheus's postgresql scrape job targets postgres_exporter:9187; if the exporter is not running all database-level metrics are missing |
| grafana | prometheus | Grafana queries Prometheus as its data source; without Prometheus running every dashboard panel shows "no data" |
| postgres_exporter | database | The exporter connects to Postgres using DATA_SOURCE_NAME; without the database it cannot authenticate and exports nothing |
| client | api (inferred) | The React frontend makes HTTP requests to the Django API for all data; if the API is down every data-fetching page fails with a network error |

### 1e. Main Entry Points

| Service | Startup File | Routes / URL Config File |
|---|---|---|
| api (Django) | learn-ops-api/manage.py | learn-ops-api/LearningPlatform/urls.py |
| client (React) | learn-ops-client/src/index.js | learn-ops-client/src/components/ApplicationViews.js |
| database (Postgres) | N/A (managed by Docker image) | N/A (no HTTP routes; accepts TCP connections on port 5432) |
| prometheus | N/A (managed by Docker image) | learn-ops-infrastructure/prometheus.yml (defines scrape targets, not HTTP routes) |
| grafana | N/A (managed by Docker image) | N/A (routing is internal to the Grafana binary) |
| postgres_exporter | N/A (managed by Docker image) | N/A (exposes a single fixed endpoint /metrics on port 9187) |

## 2. Services

| Service Name | Tech Stack (including version) | Purpose |
|---|---|---|
| database | postgres:16 | Primary relational data store for all platform data |
| api | Django REST Framework (Python) — separate repo | REST API backend; handles all business logic, authentication, and data access |
| client | React (JavaScript) — separate repo | Browser-based web frontend; the UI instructors and students interact with |
| prometheus | prom/prometheus:latest | Scrapes and stores time-series metrics from the API and database exporter |
| grafana | grafana/grafana:latest | Visualizes Prometheus metrics as dashboards for observability |
| postgres_exporter | quay.io/prometheuscommunity/postgres-exporter:latest | Translates Postgres internal stats into a Prometheus-compatible metrics endpoint |
| Valkey (planned) | Valkey message broker | Routes async messages between the API and background worker services |
| Monarch (planned) | Python — separate repo (service-monarch) | Background worker that handles GitHub and Slack integration tasks |
| Hashtagger (planned) | TBD | Processes Slack hashtag events for the platform |
| GitHub API | External service | Provides student repository and org membership data |
| Slack API | External service | Provides workspace messaging and notification integration |

## 3. System Overview

LearnOps is an education management platform built for Nashville Software School. It solves the problem of tracking student progress across a structured software development curriculum, giving instructors a single place to manage cohorts, record assessment results, and monitor where each student stands — replacing spreadsheets and disconnected tools with a purpose-built system.

From a user's perspective, the platform lets instructors create and organize courses, assign students to cohorts, record and review assessment outcomes, leave notes on individual students, and view progress dashboards at a glance. It also integrates directly with GitHub and Slack, so activity that already happens in those tools — such as repository forks, team membership, and workspace communication — is visible and actionable inside the platform without switching contexts.

The system is designed for two distinct roles: instructors and students. Instructors have staff-level access and interact with the full management interface — creating cohorts, scoring assessments, and tracking progress across the entire group. Students have a more limited view focused on their own dashboard, showing their current cohort, assessment status, and capstone information. The setup wizard reflects this separation by seeding the database with an instructor fixture on first run, establishing role-based access from the start.
