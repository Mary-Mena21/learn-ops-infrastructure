# Tech Stack Manual

## 1. System Overview
The Learning Platform is Nashville Software School's Dockerized education management system, combining a Django REST API, React frontend, 
PostgreSQL database, and GitHub/Slack integrations. It lets instructors create courses, manage cohorts, track student progress, 
and automatically provision curriculum repositories and communication channels.
It is used by NSS instructors and staff to run the technical side of coding bootcamps, while students engage with it through assigned 
GitHub repos and Slack workspaces that the system configures and monitors for each cohort.

## 2. Services

| Service Name | Tech Stack (including version) | Purpose |
| database| PostgreSQL 16 | Persistent relational data storage for the LMS
| api| Django REST API (Python)| Backend application server providing REST endpoints
| client| React (Node.js)| Frontend web application serving the user interface


## 3. Run Questions

### 3a. Config Files
| Config File | Location | Config Value | What it's for | How it's used |
| .env | learn-ops-api/ | LEARNING_GITHUB_CALLBACK | The redirect URL GitHub sends users back to after OAuth login | Django passes this to GitHub when initiating the OAuth flow; must match the callback URL registered in the GitHub OAuth app |
| .env | learn-ops-api/ | LEARN_OPS_HOST | Hostname of the PostgreSQL database container | Django's database settings use this to locate the database service on the Docker network |
| .env | learn-ops-api/ | VALKEY_HOST | Hostname of the Valkey message broker container | Used by the API to connect to Valkey and publish messages (e.g. triggering issue migration) onto pub/sub channels |

### 3b. How to Start It
| Command | What it does |
| make setup | One-time interactive setup wizard. Clones all repos, collects secrets, writes .env files, seeds the database with an instructor account. Run this first on a new machine. |
| make up | Builds Docker images and starts all services in the background.|
| make restart | Stops all running containers, then rebuilds and starts everything again. Use this when code or config changes need to be picked up. |


### 3c. Where to Access It
| Service | Port | URL |
| client | 3000 | http://localhost:3000 |
| api | 8000 | http://localhost:8000 |

### 3d. Service Dependencies
| Service | Depends On | Why |
| api | database | Django cannot serve requests without a running database to read and write application data |
| client | api | The React app has no data of its own — every action calls the backend API |


### 3e. Main Entry Points
| Service | Startup File | Routes / URL Config File |
| api | learn-ops-api/manage.py | learn-ops-api/LearningPlatform/urls.py |
| client | learn-ops-client/src/index.js | learn-ops-client/src/components/ApplicationViews.js |
| monarch | service-monarch/service/main.py | service-monarch/service/custom_logging/web_interface.py |