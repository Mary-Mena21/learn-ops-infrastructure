# System Map AI Prompts

## 1. Describe the System v1
❯ Draw the system architecture of this system. Find every service and every connection between them. For each connection note the direction and type (HTTP, database query, pub/sub event, etc.) port number and frameworks like React, Django etc should be labeled.
  Output as ASCII art diagram. Each service gets its own labeled box. Every dependency between services gets a directional arrow labeled with the connection type. Do not add anything beyond services and their connections.


## 2. Convert to a Mermaid Diagram v1
Add diagram to your memory.

Check your memory for the system diagram. Convert it to Mermaid. Use graph LR.
Rules:
- Each service is a node with a short label
- Each connection is a directed edge
- Label every edge with the connection type (HTTP, DB, pub/sub, etc.) and port if known
- Do not add anything beyond services and their connections
Output only the Mermaid code block.


## ASCII v1
        +------------------+         HTTPS :443          +------------------+
        |   React Client   |----------------------------->|   GitHub API     |
        |   (React 16)     |       OAuth 2.0 redirect     |   (External)     |
        |   :3000          |                              +------------------+
        +------------------+                                      ^
                |                                                 |
                | HTTP REST                                       | HTTPS :443
                | :8000                                           | REST (repos,
                v                                                 | collaborators,
        +------------------+         HTTPS :443          +--------+         issues)
        |   Django API     |----------------------------->|   Slack API      |
        |   (DRF/Python)   |       REST (channels,        |   (External)     |
        |   :8000          |       messages)              +------------------+
        |   :5678 debugpy  |                                      ^
        +------------------+                                      |
                |         |                                       | HTTPS :443
                |         |                                       | REST
                |         | TCP Pub/Sub                           | (notifications)
                |         | publish                               |
                |         | :6379                        +------------------+
                |         +----------------------------->|   Valkey         |
                |                                        |   (Redis-compat) |
                | SQL queries                            |   :6379          |
                | TCP :5432                              +------------------+
                |                                                 |
                v                                                 | TCP Sub
        +------------------+                                      | :6379
        |   PostgreSQL     |                                      | + Streams
        |   :5432          |                             +--------+
        +------------------+                             |
                |                              +------------------+
                | TCP :5432                    |   Monarch        |
                | DB metrics         |
                |                              |   :8080 metrics  |
                v                              |   :8081 log UI   |
        +------------------+
        |  Postgres        |                           |
        |  Exporter        |                           | HTTPS :443
        |  :9187           |                           | REST (issues)
        +------------------+                           v
                |                              +------------------+
                | HTTP scrape                  |   GitHub API     |
                | :9187                        |   (External)     |
                |                              +------------------+
                v
        +------------------+         HTTP scrape
        |   Prometheus     |<----------------------------+------------------+
        |   :9090          |         o API     |
        +------------------+                             +------------------+
                |
                | HTTP PromQL
                | :9090
                v
        +------------------+
        |   Grafana        |
        |   :3001          |
        +------------------+


        Production only:
        +------------------+     HTTPS :443     +------------------+     HTTP :8000
        |   Internet       |-------------------->|   Nginx          |-------------->| Django API |
        |                  |     (learning/      |   Reverse Proxy  |
        |                  |      lea  |     static
        |                  |      .nss.team)     |                  |-------------->| React build|
        +------------------+         

##

## 1. Describe the System  v2
❯ Read the docker-compose.yml file in this repo. Draw the system architecture based ONLY on that file.
Find every service and every connection between them. For each connection, note:
Direction (which service talks to which)
Type (HTTP, SQL query, metrics scrape, pub/sub, etc.)
Port numbers
Frameworks (React, Django, PostgreSQL, Prometheus, etc.)
Output as ASCII art diagram. Each service gets its own labeled box with its name, port, and framework. Every dependency between services gets a directional arrow labeled with the connection type.
Do not add anything beyond services and connections that exist in the docker-compose.yml file.

## 2. Convert to a Mermaid Diagram v2
Check your memory for the system diagram. Convert it to Mermaid. Use graph LR.
Rules:
Each service is a node with a short label
Each connection is a directed edge
Label every edge with the connection type (HTTP, DB, pub/sub, etc.) and port if known
Do not add anything beyond services and their connections
Output only the Mermaid code block.


## ASCII 
        +------------------+     SQL TCP :5432      +------------------+
        |       api        |----------------------->|    database      |
        |  (Django)        |                        |  (PostgreSQL 16) |
        |  :8000, :5678    |                        |  :5432           |
        +------------------+                        +------------------+
                ^                                           ^
                |                                           |
                | HTTP scrape :8000                         | SQL TCP :5432
                |                                           |
        +------------------+                       +------------------+
        |   prometheus     |                       | postgres_exporter|
        |  (Prometheus)    |                       | (PG Exporter)    |
        |  :9090           |                       | :9187            |
        +------------------+                       +------------------+
                ^
                |
                | HTTP PromQL :9090
                |
        +------------------+
        |    grafana       |
        |  (Grafana)       |
        |  :3001           |
        +------------------+


        +------------------+    Redis CLI :6379    +------------------+
        |     valkey       |<---------------------|  valkey-monitor  |
        |  (Valkey)        |                      |  (valkey-cli)    |
        |  :6379           |                      |                  |
        +------------------+                      +------------------+


        +------------------+
        |     client       |
        |  (React)         |
        |  :3000           |
        +------------------+
        (no depends_on in docker-compose.yml)

## docker-compose.yml
         Services:           Ports:           Framework:
        ├── client          :3000            React
        ├── api             :8000, :5678     Django
        ├── database        :5432            PostgreSQL 16
        ├── prometheus      :9090            Prometheus
        ├── grafana         :3001            Grafana
        └── postgres_exporter :9187          Postgres Exporter

        Connections:
        ├── client ↔ api (HTTP)
        ├── api ↔ database (SQL)
        ├── api → prometheus (metrics scraped)
        ├── database → postgres_exporter (SQL)
        ├── postgres_exporter → prometheus (scrape)
        ├── prometheus → grafana (PromQL query)

        Depends_on:
        ├── api waits for database (healthy)
        ├── prometheus waits for api
        ├── grafana waits for prometheus
        └── postgres_exporter waits for database

##

## System Diagram Mermaid style

Convert the system diagram to Mermaid. Use graph TB (top-to-bottom layout).

STYLING RULES:
- Group services into subgraphs: "Frontend Layer", "Application Layer", "Data Layer", "Monitoring Stack"
- Use emojis in node labels: 🖥️ Client, ⚙️ API, 🗄️ Database, 📊 Prometheus, 📈 Grafana, 🔌 Postgres Exporter
- Each node shows: emoji + service name + framework + port on separate lines using <br/>
- Use short edge labels: "HTTP REST", "SQL Query", "Metrics Scrape", "PromQL Query"
- Add style declarations for each node with:
  - fill: light background color
  - stroke: darker border color  
  - stroke-width: 3px
  - color: dark text color
- Use direction LR inside each subgraph so nodes sit side-by-side
- Keep arrows straight and flowing downward — no crossing lines

Output only the Mermaid code block.