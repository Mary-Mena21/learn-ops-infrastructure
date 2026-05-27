# Tech Stack (AI)

## 1. Run Questions

### 1a. Config Files

| Config File | Location | Config Value | What it's for | How it's used |
|---|---|---|---|---|
┌────────────────────┬───────────────────────────┬──────────────────┬───────────────────┬────────────────────────────────────┐
│    Config File     │         Location          │   Config Value   │   What it's for   │           How it's used            │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Name of the       │ Created at first boot; referenced  │
│ .env               │ learn-ops-infrastructure/ │ POSTGRES_DB      │ PostgreSQL        │ by the API and postgres_exporter   │
│                    │                           │                  │ database          │                                    │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│ .env               │ learn-ops-infrastructure/ │ POSTGRES_USER    │ Database username │ Created by PostgreSQL at startup;  │
│                    │                           │                  │                   │ used to authenticate connections   │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Full connection   │ Read by postgres_exporter to know  │
│ .env               │ learn-ops-infrastructure/ │ DATA_SOURCE_NAME │ string for the    │ which PostgreSQL instance to       │
│                    │                           │                  │ metrics exporter  │ scrape                             │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Name of the       │ Created at first boot; referenced  │
│ .env               │ learn-ops-infrastructure/ │ POSTGRES_DB      │ PostgreSQL        │ by the API and postgres_exporter   │
│                    │                           │                  │ database          │                                    │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│ .env               │ learn-ops-infrastructure/ │ POSTGRES_USER    │ Database username │ Created by PostgreSQL at startup;  │
│                    │                           │                  │                   │ used to authenticate connections   │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Full connection   │ Read by postgres_exporter to know  │
│ .env               │ learn-ops-infrastructure/ │ DATA_SOURCE_NAME │ string for the    │ which PostgreSQL instance to       │
│                    │                           │                  │ metrics exporter  │ scrape                             │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Docker image and  │ Pinning a version (e.g.            │
│ docker-compose.yml │ learn-ops-infrastructure/ │ image            │ version per       │ postgres:16) ensures consistent    │
│                    │                           │                  │ service           │ software across machines           │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│ docker-compose.yml │ learn-ops-infrastructure/ │ depends_on       │ Startup order     │ Prevents a service starting before │
│                    │                           │                  │ between services  │  its dependency is ready           │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Maps host ports   │ Makes services reachable from the  │
│ docker-compose.yml │ learn-ops-infrastructure/ │ ports            │ to container      │ developer's machine                │
│                    │                           │                  │ ports             │                                    │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Mounts storage    │ Persists data across restarts;     │
│ docker-compose.yml │ learn-ops-infrastructure/ │ volumes          │ into containers   │ syncs local source code into       │
│                    │                           │                  │                   │ containers                         │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ How often         │ Fires an HTTP request to every     │
│ prometheus.yml     │ learn-ops-infrastructure/ │ scrape_interval  │ Prometheus polls  │ target every 15 seconds            │
│                    │                           │                  │ each target       │                                    │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ URL path          │ Set to /metrics/metrics to match   │
│ prometheus.yml     │ learn-ops-infrastructure/ │ metrics_path     │ Prometheus hits   │ the path django-prometheus exposes │
│                    │                           │                  │ on each target    │                                    │
├────────────────────┼───────────────────────────┼──────────────────┼───────────────────┼────────────────────────────────────┤
│                    │                           │                  │ Addresses of      │ Points Prometheus at api:8000 and  │
│ prometheus.yml     │ learn-ops-infrastructure/ │ targets          │ services to       │ postgres_exporter:9187             │
│                    │                           │                  │ monitor           │                                    │
└────────────────────┴───────────────────────────┴──────────────────┴───────────────────┴────────────────────────────────────┘

### 1b. How to Start It
| Command | What it does | When to use it |
|---|---|---|
┌─────────────────┬───────────────────────────────────────────────────────────┬──────────────────────────────────────────────┐
│     Command     │                       What it does                        │                When to use it                │
├─────────────────┼───────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│                 │ Runs the first-time setup wizard — clones repos, collects │ The very first time you set up the project   │
│ make setup      │  secrets, writes .env files, and optionally starts the    │ on a new machine                             │
│                 │ stack                                                     │                                              │
├─────────────────┼───────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│ make up         │ Builds and starts all services in detached mode           │ When you want the full stack running — API,  │
│                 │                                                           │ client, database, Prometheus, and Grafana    │
├─────────────────┼───────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│ make up-api     │ Builds and starts only the api service                    │ When working on backend code only and the    │
│                 │                                                           │ React client is not needed                   │
├─────────────────┼───────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│ make            │ Builds and starts only the api and client services        │ When working on the frontend and need the    │
│ up-client-api   │                                                           │ API responding but not the monitoring stack  │
├─────────────────┼───────────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│ make restart    │ Stops all containers then rebuilds and starts everything  │ When a config or Dockerfile change isn't     │
│                 │                                                           │ being picked up and you need a clean rebuild │
└─────────────────┴───────────────────────────────────────────────────────────┴──────────────────────────────────────────────┘

### 1c. Where to Access It

| Service | Port | URL |
|---|---|---|
┌─────────────────────┬──────┬────────────────────────────────────────────────────┐
│       Service       │ Port │                        URL                         │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ database (Postgres) │ 5432 │ postgresql://localhost:5432                        │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ api (Django)        │ 8000 │ http://localhost:8000                              │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ api (debugger)      │ 5678 │ localhost:5678 (debugpy attach, not a browser URL) │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ client (React)      │ 3000 │ http://localhost:3000                              │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ prometheus          │ 9090 │ http://localhost:9090                              │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ grafana             │ 3001 │ http://localhost:3001                              │
├─────────────────────┼──────┼────────────────────────────────────────────────────┤
│ postgres_exporter   │ 9187 │ http://localhost:9187/metrics                      │
└─────────────────────┴──────┴────────────────────────────────────────────────────┘

### 1d. Service Dependencies

| Service | Depends On | Why |
|---|---|---|
┌───────────────────┬───────────────────┬──────────────────────────────────────────────────────────────────────────┐
│      Service      │    Depends On     │                                   Why                                    │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ api               │ database          │ Django runs migrations and opens a connection pool at startup; if        │
│                   │                   │ Postgres is not ready, the API process crashes immediately               │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│                   │                   │ Prometheus's django scrape job targets api:8000/metrics/metrics; if the  │
│ prometheus        │ api               │ API is not up, that job returns connection errors and no application     │
│                   │                   │ metrics are collected                                                    │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ grafana           │ prometheus        │ Grafana queries Prometheus as its data source; without Prometheus        │
│                   │                   │ running, every dashboard panel shows "no data"                           │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ postgres_exporter │ database          │ The exporter connects to Postgres using DATA_SOURCE_NAME; without the    │
│                   │                   │ database it cannot authenticate and exports nothing                      │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ prometheus        │ postgres_exporter │ Prometheus's postgresql scrape job targets postgres_exporter:9187; if    │
│                   │                   │ the exporter is not running, all database-level metrics are missing      │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ client            │ api               │ The React frontend makes HTTP requests to the Django API for all         │
│                   │                   │ apPI every data-fetching page fails                                      │
├───────────────────┼───────────────────┼──────────────────────────────────────────────────────────────────────────┤
│                   │ database          │ Aloriginate from the database via                                        │
│ grafana           │ (inferred)        │ postgres_exporter → prometheus; no database means no meaningful data in  │
│                   │                   │ any dashboard                                                            │
└───────────────────┴───────────────────┴──────────────────────────────────────────────────────────────────────────┘

### 1e. Main Entry Points

| Service | Startup File | Routes / URL Config File |
|---|---|---|
┌───────────────────┬───────────────────────────────┬──────────────────────────────────────────────────────────────┐
│      Service      │         Startup File          │                   Routes / URL Config File                   │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ api (Django)      │ learn-ops-api/manage.py       │ learn-ops-api/LearningPlatform/urls.py                       │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ client (React)    │ learn-ops-client/src/index.js │ learn-ops-client/src/components/ApplicationViews.js          │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ database          │ N/A (managed by Docker image) │ N/A (no HTTP routes; accepts TCP connections on port 5432)   │
│ (Postgres)        │                               │                                                              │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ prometheus        │ N/A (managed by Docker image) │ learn-ops-infrastructure/prometheus.yml (scrape targets, not │
│                   │                               │  HTTP routes)                                                │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ grafana           │ N/A (managed by Docker image) │ N/A (routing is internal to the Grafana binary)              │
├───────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ postgres_exporter │ N/A (managed by Docker image) │ N/A (exposes a single fixed endpoint /metrics on port 9187)  │
└───────────────────┴───────────────────────────────┴──────────────────────────────────────────────────────────────┘

## 2. Services

| Service Name | Tech Stack (including version) | Purpose |
|---|---|---|
┌─────────────────────┬──────────────────────────────────────────────────────┬──────────────────────────────────────┐
│       Service       │                      Tech Stack                      │               Purpose                │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ api                 │ Django (Python)                                      │ REST API backend                     │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ client              │ React (JavaScript)                                   │ Web frontend                         │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ database            │ PostgreSQL 16                                        │ Primary data store                   │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ prometheus          │ prom/prometheus:latest                               │ Scrapes and stores metrics           │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ grafana             │ grafana/grafana:latest                               │ Visualizes metrics as dashboards     │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ postgres_exporter   │ quay.io/prometheuscommunity/postgres-exporter:latest │ Exposes Postgres metrics for         │
│                     │                                                      │ Prometheus                           │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ Valkey (planned)    │ Valkey broker                                        │ Routes messages between services     │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ Monarch (planned)   │ Python                                               │ Handles GitHub and Slack background  │
│                     │                                                      │ tasks                                │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ Hashtagger          │ TBD                                                  │ Processes Slack hashtag events       │
│ (planned)           │                                                      │                                      │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ GitHub API          │ External                                             │ Student repo and org membership data │
├─────────────────────┼──────────────────────────────────────────────────────┼──────────────────────────────────────┤
│ Slack API           │ External                                             │ Messaging and workspace integration  │
└─────────────────────┴──────────────────────────────────────────────────────┴──────────────────────────────────────┘

## 3. System Overview
LearnOps is an education management platform built for Nashville Software School. It solves the problem of tracking student progress across a structured software development curriculum — giving instructors a central place to manage cohorts, record assessment results, and monitor where each student stands instead of relying on spreadsheets or disconnected tools.

From a user's perspective, the platform lets instructors create and manage courses, organize students into cohorts, record assessment outcomes, leave notes on individual students, and view progress dashboards. It also integrates directly with GitHub and Slack, so activity that already happens in those tools — like repo forks and team communication — is visible and actionable inside the platform without switching contexts.

The system supports two distinct roles: instructors and students. Instructors have staff-level access and interact with the full management interface — creating cohorts, scoring assessments, and tracking progress across the entire cohort. Students have a more limited view focused on their own dashboard, showing their current cohort, assessment status, and capstone information. The setup script reflects this separation by seeding the database with an instructor fixture on first run, establishing role-based access from the start.
