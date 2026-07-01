# Data Model

## 1. Database Diagram

![Diagram](./data-model.png)

## 2. Database Info

**Database type:** PostgreSQL 16

**ORM:** Django ORM

## 3. Model to Table Mapping

| Model Name | Table Name |
|------------|------------|
| Course     | learningapi_course |
| Book       | learningapi_book |
| Project    | learningapi_project |
| Cohort     | learningapi_cohort |
| NssUser    | learningapi_nssuser |
| CohortInfo | learningapi_cohortinfo |
| NssUserCohort | learningapi_nssusercohort |
| GroupProjectRepository | learningapi_groupprojectrepository |
| Tag        | learningapi_tag |

| Property Name | Column Name | Data Type |
|---------------|-------------|-----------|
| Book.name     | name        | varchar(75) |
| Book.course_id| course_id   | integer (FK) |
| Book.description | description | text |
| Book.index    | index       | integer |
| Project.name  | name        | varchar(55) |
| Project.implementation_url | implementation_url | varchar(256) |
| Project.client_template_url | client_template_url | varchar(200) |
| Project.api_template_url | api_template_url | varchar(200) |
| Project.book_id | book_id | integer (FK) |
| Project.index | index | integer |
| Project.active | active | boolean |
| Project.is_group_project | is_group_project | boolean |
| Cohort.name   | name        | varchar(55) |
| Cohort.slack_channel | slack_channel | varchar(55) |
| Cohort.start_date | start_date | date |
| Cohort.end_date | end_date | date |
| Cohort.break_start_date | break_start_date | date |
| Cohort.break_end_date | break_end_date | date |
| Cohort.active | active | boolean |
| NssUser.user_id | user_id | integer (FK, OneToOne) |
| NssUser.slack_handle | slack_handle | varchar(55) |
| NssUser.github_handle | github_handle | varchar(55) |
| CohortInfo.cohort_id | cohort_id | integer (FK, OneToOne) |
| CohortInfo.student_organization_url | student_organization_url | varchar(255) |
| CohortInfo.github_classroom_url | github_classroom_url | varchar(255) |
| CohortInfo.attendance_sheet_url | attendance_sheet_url | varchar(255) |
| CohortInfo.client_course_url | client_course_url | varchar(255) |
| CohortInfo.server_course_url | server_course_url | varchar(255) |
| CohortInfo.zoom_url | zoom_url | varchar(255) |
| NssUserCohort.nss_user_id | nss_user_id | integer (FK) |
| NssUserCohort.cohort_id | cohort_id | integer (FK) |
| NssUserCohort.is_github_org_member | is_github_org_member | boolean |
| GroupProjectRepository.team_id | team_id | integer (FK) |
| GroupProjectRepository.project_id | project_id | integer (FK) |
| GroupProjectRepository.repository | repository | varchar(255) |
| Tag.name      | name        | varchar(25) |

## 4. Relationship Examples

**One-to-one** (field name: user)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| NssUser    | learningapi_nssuser | id | user_id |
| User (Django auth) | auth_user | id | id (referenced by user_id) |

**One-to-one** (field name: cohort)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| CohortInfo | learningapi_cohortinfo | id | cohort_id |
| Cohort     | learningapi_cohort | id | id (referenced by cohort_id) |

**One-to-many** (field name: course)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| Course     | learningapi_course | id | id (referenced by course_id) |
| Book       | learningapi_book | id | course_id |

**One-to-many** (field name: book)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| Book       | learningapi_book | id | id (referenced by book_id) |
| Project    | learningapi_project | id | book_id |

**One-to-many** (field name: nss_user)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| NssUser    | learningapi_nssuser | id | id (referenced by nss_user_id) |
| NssUserCohort | learningapi_nssusercohort | id | nss_user_id |

**One-to-many** (field name: cohort)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| Cohort     | learningapi_cohort | id | id (referenced by cohort_id) |
| NssUserCohort | learningapi_nssusercohort | id | cohort_id |

**One-to-many** (field name: team)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| StudentTeam | learningapi_studentteam | id | id (referenced by team_id) |
| GroupProjectRepository | learningapi_groupprojectrepository | id | team_id |

**One-to-many** (field name: project)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| Project    | learningapi_project | id | id (referenced by project_id) |
| GroupProjectRepository | learningapi_groupprojectrepository | id | project_id |

**Many-to-many** (field name: )

&gt; **Note:** I could not find a `ManyToManyField` in the model files I successfully retrieved. You may need to check the `Course` and `StudentTeam` models (which I could not access) for a `ManyToManyField`. Common examples might be:
&gt; - `Course` → `tags` (ManyToMany to `Tag`)
&gt; - `Cohort` → `courses` (ManyToMany or ForeignKey)
&gt; - `StudentTeam` → `members` (ManyToMany to `NssUser`)

| Model Name | Table Name | PK Column | FK Column |
|------------|------------|-----------|-----------|
| (check Course model) | | | |
| (check related model) | | | |
| (junction) | | | |