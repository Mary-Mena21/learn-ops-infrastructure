# System Map AI Prompts

## 1. Describe the System
❯ Draw the system architecture of this system. Find every service and every connection between them. For each connection note the direction and type (HTTP, database query, pub/sub event, etc.) port number and frameworks like React, Django etc should be labeled.
  Output as ASCII art diagram. Each service gets its own labeled box. Every dependency between services gets a directional arrow labeled with the connection type. Do not add anything beyond services and their connections.


## 2. Convert to a Mermaid Diagram
Add diagram to your memory.

Check your memory for the system diagram. Convert it to Mermaid. Use graph LR.
Rules:
- Each service is a node with a short label
- Each connection is a directed edge
- Label every edge with the connection type (HTTP, DB, pub/sub, etc.) and port if known
- Do not add anything beyond services and their connections
Output only the Mermaid code block.


## ASCII

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