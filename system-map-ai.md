# System Map (AI)

## 1. System Diagram v1
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

## 1. System Diagram v2
```mermaid
graph LR
    api[Django API :8000]
    database[PostgreSQL :5432]
    prometheus[Prometheus :9090]
    grafana[Grafana :3001]
    postgres_exporter[PG Exporter :9187]
    valkey[Valkey :6379]
    valkey_monitor[valkey-cli monitor]
    client[React Client :3000]

    api -->|SQL TCP 5432| database
    postgres_exporter -->|SQL TCP 5432| database
    prometheus -->|HTTP scrape 8000| api
    grafana -->|HTTP PromQL 9090| prometheus
    valkey_monitor -->|Redis CLI 6379| valkey
```


##
```mermaid
graph TB
    subgraph Frontend["Frontend Layer"]
        direction LR
        client["🖥️ Client<br/>React<br/>:3000"]
    end

    subgraph Application["Application Layer"]
        direction LR
        api["⚙️ API<br/>Django<br/>:8000"]
    end

    subgraph Data["Data Layer"]
        direction LR
        database["🗄️ Database<br/>PostgreSQL 16<br/>:5432"]
        valkey["💾 Valkey<br/>Valkey<br/>:6379"]
    end

    subgraph Monitoring["Monitoring Stack"]
        direction LR
        prometheus["📊 Prometheus<br/>Prometheus<br/>:9090"]
        grafana["📈 Grafana<br/>Grafana<br/>:3001"]
        pg_exporter["🔌 PG Exporter<br/>postgres-exporter<br/>:9187"]
        valkey_monitor["👁️ Valkey Monitor<br/>valkey-cli<br/>monitor"]
    end

    api -->|SQL Query| database
    pg_exporter -->|SQL Query| database
    prometheus -->|Metri
    grafana -->|PromQL Query| prometheus
    valkey_monitor -->|Redis CLI| valkey

    style client fill:#dbeafe,stroke:#3b82f6,stroke-width:3px,color:#1e3a5f
    style api fill:#dcfce7,stroke:#22c55e,stroke-width:3px,color:#14532d
    style database fill:-width:3px,color:#713f12
    style valkey fill:#fef9c3,stroke:#eab308,stroke-width:3px,color:#713f12
    style prometheus fill:#f3e8ff,stroke:#a855f7,stroke-width:3px,color:#3b0764
    style grafana fill:#f3e8ff,stroke:#a855f7,stroke-width:3px,color:#3b0764
    style pg_exporter fill:#f3e8ff,stroke:#a855f7,stroke-width:3px,color:#3b0764
    style valkey_monitorfill:#f3e8ff,stroke:#a855f7,stroke-width:3px,color:#3b0764
```