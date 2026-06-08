# learn-ops-api: Service Exploration

## 1. Top-level folders in `learn-ops-api`

| Folder          | Why does this folder need to exist? |
|-----------------|-------------------------------------|
|LearningAPI      | It holds the Module, Packages and classes (Main Django app) |
|LearningPlatform | It contains the configuration package (Django project config)|
|LogViewer        | log viewing interface               |

## 2. Folders inside `LearningAPI`

| Folder        | What responsibility does it own and why? |
|---------------|----------------------------------------------------------|
| fixtures      | JSON test data files                          |
| migrations    | Database change history|
| models        | It can contain functions, variables, and class definitions|
| serializers   | Converts Python model objects to/from JSON for API responses|

## 3. What is the Pipfile?
These are the Python packages we need

## 4. Key packages in the Pipfile

| Package | What functionality does it provide and why? |
|---------|----------------------------------|
| django |This is the backend.The main Python web framework|
| djangorestframework |Adds REST API tools on top of Django |
| django-allauth |Turns Django into a proper API server |

## 5. What does `decorators.py` do?
It verifies authentication and authorization

## 6. What is a serializer, and what serializers are defined here?
A serializer is a translator between your Python code and JSON

## 7. Models and what they represent

| Model | Real-world thing it represents |
|-------|-------------------------------|
| book.py | Model for books in the courses - "Python Crash Course" book|
| capstone_timeline | Model for recording history of capstone proposal - "Proposal due Week 3"|
| cohort_course| Links between cohorts and courses - Cohort 47 takes Python 101 |

## 8. Views vs. viewsets

| Type | Example class | File path |
|------|--------------|-----------|
| View |login_user |LearningAPI/views/auth.py |
| ViewSet |StudentViewSet |LearningAPI/views/student_view.py |

## 9. Serializers paired with their models

| Serializer            | Model     | Link |
|-----------------------|-----------|------|
| CapstoneSerializer    |Capstone   |LearningAPI/serializers/capstone_serializer.py      |
| CohortSerializer      |Cohort     |LearningAPI/serializers/cohort_serializer.py      |

## 10. What replaces the Templates and why?

React replaces Django templates because the frontend now runs in the browser, not on the server. Django only sends JSON data through the API.
-----------------------------------------------------

packages
modules
layers

## Step 1: Explore the learn-ops-api (Django) projects organization
 Find answers to these questions using Django/python docs, make sure to use the versions you identified earlier in the course:

# 1- List top level directories at the root of learn-ops-api. For each folder, explain what purpose it serves in this project.

# 2- List top level directories inside learn-ops-api/LearningAPI. For each folder, explain what responsibility it owns.

# 3- Open Pipfile. Why does this file exist? What is it's purpose?

# 4- Look up django, djangorestframework, and django-allauth in the Pipfile. For each: what functonality does it provide. Why does the project import it?

# 5- Open LearningAPI/decorators.py. What is a decorator and how is it used?

# 6- Open LearningAPI/serializers.py. What do serializers do? Why does a Django REST API need serializers at all? Explain how it fits into the request response cycle.

# 7- Open the models folder inside LearningAPI. What is a Model? Why does it exist? Pick one model and answer what real-world thing it represents and why the API needs to track that data.

# 8- In Django, a view handles one URL and a viewset handles a full set of routes for a resource. Find one example of each: why would you choose a viewset over a plain view?

# 9- Pick a serializer and find the model it belongs to. Explain how this serializer is used and what functionality it's providing.

# 10- Django's pattern is Model-Template-View. This project does not have HTML templates. What takes the template's job here, and why does that make sense for this app?
app.diagram.net