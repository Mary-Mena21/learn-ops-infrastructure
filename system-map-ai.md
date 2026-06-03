# System Map (AI)

## 1. System Diagram
```mermaid
graph LR
    Client["React Client :3000"]
    API["Django API :8000"]
    PG["PostgreSQL :5432"]
    Valkey["Valkey :6379"]
    Monarch["Monarch :8080/:8081"]
    Prometheus["Prometheus :9090"]
    PGExp["Postgres Exporter :9187"]
    Grafana["Grafana :3001"]
    Nginx["Nginx :443"]
    GitHub["GitHub API :443"]
    Slack["Slack API :443"]

    Client -->|"HTTP REST :8000"| API
    Client -->|"HTTPS OAuth2 :443"| GitHub
    API -->|"SQL TCP :5432"| PG
    API -->|"HTTPS REST :443"| GitHub
    API -->|"HTTPS REST :443"| Slack
    API -->|"Pub/Sub publish :6379"| Valkey
    Valkey -->|"Pub/Sub subscribe :6379"| Monarch
    Monarch -->|"HTTPS REST :443"| GitHub
    Monarch -->|"HTTPS REST :443"| Slack
    Monarch -->|"Redis Streams :6379"| Valkey
    PG -->|"SQL TCP :5432"| PGExp
    PGExp -->|"HTTP scrape :9187"| Prometheus
    API -->|"HTTP scrape :8000"| Prometheus
    Monarch -->|"HTTP scrape :8080"| Prometheus
    Prometheus -->|"HTTP PromQL :9090"| Grafana
    Nginx -->|"HTTP :8000"| API
    Nginx -->|"static files"| Client
    ```