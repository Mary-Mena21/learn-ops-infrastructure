# Data Model AI Prompts

## 1. Find the Database Connection Details
I need to connect to the database for this system using pgAdmin.
Find the host, port, database name, username, and password.
Look in the environment files and docker-compose configuration.
Tell me which file each value comes from.

"I am working in the ~/workspace/lms/learn-ops-api directory. This is a Django project. Please find the database connection details including engine, name, user, password, host, and port. Look in settings.py and any .env files or environment variable templates. Show me the exact file paths where you found each value."

## 2. Identify the Database Type
What database engine does this application use?
Find where that is declared in the project files and tell me the version.

"In this Django project at ~/workspace/lms/learn-ops-api, what database engine is configured in the DATABASES setting in settings.py? Also check if there are any database-specific configuration files or Docker compose files that reveal the database type."

## 3. Map the ORM to the Database
How does this application communicate with the database? Find the ORM it uses
and where the database connection is configured. Include the ENGINE value from settings.

Then open LearningAPI/models/ and pick one model. Show me the Python field names
next to the SQL column names and their data types.

Finally, open LearningAPI/views/book_view.py and find the create method.
What does book.save() actually do - what SQL statement does the ORM generate?

"This is a Django project in ~/workspace/lms/learn-ops-api. Please identify the ORM being used. Then pick one model (such as User, Cohort, Course, or Book) and show me: (1) the Python model class with its properties, (2) the corresponding database table name and columns with their data types, and (3) find any create method or view that calls .save() on this model and explain what happens under the hood when save() is called."

## 4. Generate a Database Diagram
Generate an entity-relationship diagram of the database as a Mermaid erDiagram.
Look at all the model files in LearningAPI/models/.
Include every table, every field, and every relationship between tables.
Output only the Mermaid code block.

"In ~/workspace/lms/learn-ops-api, generate a complete Mermaid erDiagram of all Django models. Look in all apps' models.py files. Include every table, every field with types, and every relationship (ForeignKey, OneToOneField, ManyToManyField). Show cardinalities correctly."

## 5. Find Relationship Examples
Find one example each of a one-to-one, one-to-many, and many-to-many relationship
in the Django models in LearningAPI/models/.
For each example give me the file path and the name of the field that defines the relationship.

"Search through all models.py files in ~/workspace/lms/learn-ops-api and find one concrete example each of: a OneToOneField (one-to-one), a ForeignKey (one-to-many), and a ManyToManyField (many-to-many). For each, give me the exact file path, the model class name, and the field name."








