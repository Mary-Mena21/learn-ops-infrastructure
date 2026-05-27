# Tech Stack AI Prompts
### Global Rules for All Outputs
When producing markdown tables, you MUST follow these rules. Breaking any of them will corrupt the file on GitHub:
1. Use standard GitHub-flavored markdown tables ONLY. Delimiters are | (pipe) and --- (dash).
2. NEVER use ASCII box-drawing characters. Do not include ┌, ┐, └, ┘, ─, │, ├, ┤, ┬, ┴, ┼, or any Unicode box-drawing glyphs.
3. Each table row = exactly one line. No newline characters inside a cell. If you need a line break, use a <br> tag.
4. No blank lines between the header row, the |---|---|---| separator, and the data rows.
5. Column counts must match. The separator line must have the same number of --- segments as the header has columns.
6. No leading/trailing pipes on their own lines. Every pipe must belong to a complete row.

## 1. Run Questions

### 1a. Config Files
Look at the config and environment files in this repo: .env, docker-compose.yml, and prometheus.yml. For each file, identify at least 3 config values and produce a markdown table with exactly these columns: Config File, Location, Config Value, What it's for, How it's used.
Critical constraint: Record only the config variable name (e.g., POSTGRES_USER), never the actual secret value. Do not write POSTGRES_USER=learnops; write POSTGRES_USER.

CRITICAL TABLE RULES — DO NOT VIOLATE:
* Use | and --- ONLY. Do NOT use ASCII box-drawing characters (┌─┬─┐│).
* One line per row. No line breaks inside cells. Use <br> if needed.
* Record only the config variable name (e.g., POSTGRES_USER), never the actual secret value.

### 1b. How to Start It
Find the Makefile at the root of this repo and read its targets. List only the targets relevant for getting the system running (not cleanup or utility targets). For each one, produce output in this format: the command name, what it does, and when to use it. Then add a paragraph explaining how the up* targets differ from each other and from make setup.

This section does NOT need a table; use plain text with a table only if it helps readability. If you do use a table, obey the Global Rules above.

### 1c. Where to Access It
Find the port mapping for each service. Produce a markdown table with exactly these columns: Service, Port, URL. The URL should be the localhost address a developer would type into their browser or use to connect. Include every service that exposes a port.

CRITICAL TABLE RULES — DO NOT VIOLATE:
* Use | and --- ONLY. Do NOT use ASCII box-drawing characters (┌─┬─┐│).
* One line per row. No line breaks inside cells. Use <br> if needed.

### 1d. Service Dependencies
Identify the depends_on relationships. Also infer any functional dependencies (where one service needs another to do its job, even if not explicitly declared in depends_on). Produce a markdown table with exactly these columns: Service, Depends On, Why. The "Why" column must explain specifically what functionality breaks if the dependency is not running—not just repeat the service name.

CRITICAL TABLE RULES — DO NOT VIOLATE:
* Use | and --- ONLY. Do NOT use ASCII box-drawing characters (┌─┬─┐│).
* One line per row. No line breaks inside cells. Use <br> if needed.

### 1e. Main Entry Points
For each service in this system, find two different things: (1) the startup file where the process boots, and (2) the routes or URL configuration file where incoming requests are handled. These must be different files when possible. Produce a markdown table with exactly these columns: Service, Startup File, Routes / URL Config File. If a service doesn't have one of these, write "N/A". Look for files like manage.py, wsgi.py, urls.py, index.js, main.jsx, App.js, router.js, or similar depending on the tech stack.

CRITICAL TABLE RULES — DO NOT VIOLATE:
* Use | and --- ONLY. Do NOT use ASCII box-drawing characters (┌─┬─┐│).
* One line per row. No line breaks inside cells. Use <br> if needed.

## 2. Services
Look at docker-compose.yml, README.md, and SETUP_README.md. Identify every service in the system. Produce a markdown table with exactly these columns: Service Name, Tech Stack (including version), and Purpose. Include the version number if it appears in docker-compose.yml (e.g., postgres:16, prom/prometheus:latest). If a service's source code is in a separate repo not present here, note its tech stack as best you can from the documentation.

CRITICAL TABLE RULES — DO NOT VIOLATE:
* Use | and --- ONLY. Do NOT use ASCII box-drawing characters (┌─┬─┐│).
* One line per row. No line breaks inside cells. Use <br> if needed.

## 3. System Overview
Write a system overview section with exactly 3 paragraphs:
1.Describe what kind of application this is and what problem it solves.
2.Describe its main features from a user's perspective.
3.Describe who uses it and whether different roles interact with it differently.
Base this on README.md, SETUP_README.md, and the docker-compose.yml service definitions.


### Table Form [NOT-USED]
 Reformat your previous answer as a markdown table with these exact columns: [col1], [col2], [col3]. Do not add any introductory text—just the table.

 Each table row must be exactly one line with no line breaks inside cells. Use <br> if a cell needs multiple lines. Keep descriptions under 10 words when possible. Record only the config variable name, never the actual secret value.