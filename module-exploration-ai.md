# learn-ops-api: AI-Assisted Exploration

## 1. Top-level folders in `learn-ops-api`

| Folder            | Why does this folder need to exist? |
|-------------------|------------------------------------------|
| LearningAPI/      │ The main Django app. All business logic,database models, API endpoints, and tests live here.|
| LearningPlatform/ │ The Django project package. Holds settings, the root URL config, and the WSGI/ASGI entry points that boot the server.|
| LogViewer/        │ A separate Django app that serves the log-browsing web UI. Kept apart from LearningAPI/ because it has a different responsibility.|
| config/           │ Nginx reverse proxy configs for production.Needed so the server knows how to route traffic to the API and serve the built React  frontend.|
| logs/             │ Receives runtime log output written by the application process. Exists so logs are written to a known, persistent location outside the source tree.|
| static/           │ Source static asset files before collection. Django requires a source directory to copy from. |
| staticfiles/      │ Output of collectstatic. This is what Nginx actually serves in production. Kept separate so the web server never needs to touch the source directory|
| templates/        | Django HTML templates for any server-rendered pages (e.g. the log viewer UI). Separated from Python code because Django's template loader expects a dedicated directory.│



## 2. Folders inside `LearningAPI`

| Folder      | What responsibility does it own and why? |
|-------------|------------------------------------------|
|             |                                          |
|models/      |Defines the database schema. Split into three sub-packages — people/ (users, cohorts, teams, assessments), coursework/ (courses, books, projects, capstones), and skill/(learning records, core skills, weights) — so related tables are grouped by domain instead of piled into one file.|
|             |                                          |
|views/       |What responsibility dAPI endpoint handler, one file per resource. Also contains sub-folders for GitHub OAuth and GitHub integration logic.|
|             |                                          |
|serializers/ |Controls how model instances are translated to and from JSON. Keeps that conversion logic out of the views.|
|             |                                          |
|migrations/  |Tracks every schema change to the database in order. Django requires this directory to know what has and hasn't been applied to the database.|
|             |                                          |
|fixtures/    |Holds JSON seed data files, one per model, used to populate a fresh database with realistic data for development and testing.|
|             |                                          |
|tests/|Contains all automated tests. Kept here so Django's test runner can discover them automatically.|
|             |                                          |


## 3. What is the Pipfile?

A Pipfile is the dependency manifest for Pipenv, Python's package manager. It serves the same role that package.json plays in Node projects.

It declares:
- [packages] — libraries the app needs to run in production
- [dev-packages] — tools only needed during development (linting, testing, debugging)
- [requires] — the exact Python version the project expects (3.11.11)
- [scripts] — shortcuts for common commands (e.g. pipenv run migrate instead of pipenv run python3 manage.py migrate)

When you run pipenv install, Pipenv reads this file, resolves all versions, and writes a Pipfile.lock that pins every dependency to an exact version for reproducible installs.


## 4. Key packages

| Package             | What functionality does it provide and why? |
|---------------------|---------------------------------------------|
| django              |Django is the web framework the entire API is built on. It provides the request/response cycle, the ORM that maps Python classes to database tables, the migration system, the admin panel, URL routing, and the manage.py command runner. Without Django there is no app. |
|                     |                                             |
| djangorestframework |Django alone handles HTML pages. Django REST Framework (DRF) extends it to build JSON APIs. It adds serializers (which you saw in LearningAPI/serializers/), class-based API views, authentication handling, and automatic HTTP method routing. Every endpoint in LearningAPI/views/ is built on top of DRF. |
|                     |                                             |
| django-allauth      |This handles third-party authentication — specifically GitHub OAuth in this project. When an instructor logs in via their GitHub account, django-allauth manages the OAuth redirect flow, exchanges the token with GitHub, and creates or links the local user account. It is pinned to 0.54.0 (not *) because its API changed significantly across versions, so the project locks to a known-good release. |
|                     |                                             |


## 5. What does `decorators.py` do?
Decorators

A decorator is a function that wraps another function to add behavior before or after it runs, without changing the function's own code. In Python, you apply one using the @ symbol above a function definition.

In decorators.py, there are two: @is_instructor() and @is_staff(). Both work the same way — before the real view function runs, the wrapper checks whether the logged-in user belongs to a specific Django group ('Instructors' or 'Staff'). If yes, the original function runs normally. If no, it immediately returns a 401 Unauthorized response and the view never executes.

This means any view that handles instructor-only actions can just add @is_instructor() on top instead of repeating the same group-check logic inside every single view file. The check lives in one place and can be reused everywhere.

## 6. What is a serializer, and how does it fit the request/response cycle?
Serializers

When a request arrives at the API, the data travels as raw JSON. When the API sends a response, it needs to turn a Python object back into JSON. A serializer sits in the middle and handles both directions:

- Incoming (write): validates and converts JSON fields into a Python object ready to save to the database
- Outgoing (read): converts a Django model instance into a JSON-friendly dictionary

NssUserSerializer is a good example. It points at the NssUser model and declares exactly which fields get included in the output: url, slack_handle, github_handle, mentor, user. If a field isn't listed here, it never appears in the API response — even if it exists on the model. This gives the API control over what data it exposes.

CohortSerializer uses fields = '__all__', which means every column on the Cohort table gets serialized. Quicker to write, but exposes everything.


## 7. One model and what it represents
Models

A Django model is a Python class where each attribute maps to a column in a database table. Django reads these classes and uses them to create the actual tables, run queries, and enforce data types — you write Python, Django handles the SQL.

Cohort is a good one to look at. The real-world thing it represents is a class of students moving through the bootcamp together — for example, "Cohort 58, starting January 2026."

The model tracks:
- name and slack_channel — how to identify and communicate with this group
- start_date, end_date — the full program window
- break_start_date, break_end_date — a mid-program break period
- active — whether this cohort is currently in session

The API needs this data because almost everything else in the system is scoped to a cohort. Assessments belong to students who belong to a cohort. GitHub repos are created per team per cohort. Slack channels are created per cohort. Without Cohort as a foundational record, there is no way to organize students, track progress, or trigger the automated workflows that set up each new class.


## 8. Views vs. viewsets

| Type | Example class | About it | When to use it |
|------|---------------|----------|----------------|
| View |notify.py|notify is a plain function decorated with @api_view(['POST']). It handles exactly one URL and one HTTP method. The entire function is the handler — read the request, do the work, return a response. There is no structure beyond that.| When to choose this: one-off actions that don't map to a resource. Sending a Slack notification is not a "create, read, update, delete" operation on a database record — it is a single side-effect triggered by a POST. A plain view is the right fit because there is nothing to list, nothing to retrieve by ID, nothing to delete. |
|         |            |           |                     |
| ViewSet |CohortViewSet in cohort_view.py |CohortViewSet is a class where each method (create, retrieve, list, update, destroy) maps to a standard HTTP operation on the /cohorts resource. DRF's router wires these up automatically: GET /cohorts/ calls list, POST /cohorts/ calls create, GET /cohorts/4/ calls retrieve, and so on. It also has @action methods for non-standard operations like assign and migrate, which get their own sub-URLs (/cohorts/4/assign/).| When to choose this: any resource that has a full lifecycle. Cohorts get created, listed, retrieved, updated, and deleted. Bundling all of that into one class means the routing, permissions, and serializer stay co-located rather than scattered across multiple files. |
|         |            |           |                     |


## 9. What replaces templates and why?
Django's Model-Template-View pattern

Django's MTV pattern splits responsibilities into three layers:

- Model — owns the data and the database schema
- Template — owns how the data is presented to a user (HTML)
- View — sits in between: receives a request, asks the model for data, hands it to the template, returns the result

The pattern was designed for server-rendered web pages, where the server builds a complete HTML document and sends it to the browser.

---
What takes the template's role here

In this project the serializer takes the template's role.

In a traditional Django app, the template decides which fields to show, how to format them, and what the output looks like. In a REST API, the serializer makes exactly those same decisions — but for JSON instead of HTML. CohortSerializelude, adds computedfields like coaches and attendance_sheet_url, and controls how nested objects like courses are shaped in the output.

This makes sense because the API's job is to deliver structured data,  not rendered pages. Tthat turns that datainto what a user actually sees. The serializer produces clean, predictable JSON; the client decides what to do with it. Keeping those two responsibilities ontend and backendindependently changeable.