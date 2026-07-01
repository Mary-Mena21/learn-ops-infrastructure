# Data Model (AI)

## 1. Database Diagram

```mermaid
erDiagram

    User ||--|| NssUser : "extends"
    NssUser ||--o| StudentPersonality : "personality"
    NssUser ||--o{ NssUserCohort : "assigned_cohorts"
    NssUser ||--o{ StudentProject : "projects"
    NssUser ||--o{ StudentAssessment : "assessments"
    NssUser ||--o{ LearningRecord : "learning_records"
    NssUser ||--o{ CoreSkillRecord : "core_skills"
    NssUser ||--o{ StudentNote : "notes"
    NssUser ||--o{ Capstone : "capstones"
    NssUser ||--o{ NSSUserTeam : "teams"
    Cohort ||--o| CohortInfo : "info"
    Cohort ||--o{ NssUserCohort : "members"
    Cohort ||--o{ CohortCourse : "courses"
    Cohort ||--o{ StudentTeam : "teams"
    Cohort ||--o{ CohortEvent : "events"
    CohortEvent }o--|| CohortEventType : "event_typ
    Course ||--o{ CohortCourse : "cohorts"
    Course ||--o{ Book : "books"
    Book ||--o{ Project : "child_projects"
    Book ||--o{ Assessment : "assessments"
    Project ||--o{ StudentProject : "students"
    Project ||--o{ ProjectTag : "projecttags"
    ProjectTag }o--|| Tag : "tag"
    Assessment ||--o{ StudentAssessment : "students"
    Assessment ||--o{ AssessmentWeight : "weight_assignments"
    Assessment ||--o{ AssessmentObjective : "objectives"
    AssessmentWeight }o--|| LearningWeight : "weight"
    AssessmentObjective }o--|| LearningObjective :
    LearningObjective }o--|| TaxonomyLevel : "bloom_level"
    LearningWeight ||--o{ LearningRecord : "records"
    LearningRecord ||--o{ LearningRecordEntry : "entries"
    CoreSkill ||--o{ CoreSkillRecord : "records"
    CoreSkillRecord ||--o{ CoreSkillRecordEntry : "notes"
    StudentTeam ||--o{ NSSUserTeam : "students"
    StudentTeam ||--o{ GroupProjectRepository : "repositories"
    Capstone ||--o{ CapstoneTimeline : "statuses"
    CapstoneTimeline }o--|| ProposalStatus : "status"
    LightningExercise ||--o{ LightningTag : "lightningtags"
    LightningTag }o--|| Tag : "tag"


## 2. Database Info

**Database type: PostgreSQL**

**ORM: Django ORM**

## 3. Model to Table Mapping

|        Model Name         |              Table Name               |
|---------------------------|---------------------------------------|
| NssUser                   | LearningAPI_nssuser                   |
| StudentPersonality        | LearningAPI_studentpersonality        |
| NssUserCohort             | LearningAPI_nssusercohort             |
| Cohort                    | LearningAPI_cohort                    |
| CohortInfo                | LearningAPI_cohortinfo                |
| CohortEvent               | LearningAPI_cohortevent               |
| CohortEventType           | LearningAPI_cohorteventtype           |
| CohortGithubProject       | LearningAPI_cohortgithubproject       |
| StudentTeam               | LearningAPI_studentteam               |
| NSSUserTeam               | LearningAPI_nssuserteam               |
| StudentNote               | LearningAPI_studentnote               |
| StudentNoteType           | LearningAPI_studentnotetype           |
| StudentTag                | LearningAPI_studenttag                |
| StudentMentor             | LearningAPI_studentmentor             |
| OneOnOneNote              | LearningAPI_oneononenote              |
| Assessment                | LearningAPI_assessment                |
| AssessmentObjective       | LearningAPI_assessmentobjective       |
| AssessmentWeight          | LearningAPI_assessmentweight          |
| StudentAssessment         | LearningAPI_studentassessment         |
| StudentAssessmentStatus   | LearningAPI_studentassessmentstatus   |
| Opportunity               | LearningAPI_opportunity               |
| OpportunityUser           | LearningAPI_opportunityuser           |
| GroupProjectRepository    | LearningAPI_groupprojectrepository    |
| Course                    | LearningAPI_course                    |
| Book                      | LearningAPI_book                      |
| Project                   | LearningAPI_project                   |
| StudentProject            | LearningAPI_studentproject            |
| ProjectNote               | LearningAPI_projectnote               |
| ProjectTag                | LearningAPI_projecttag                |
| CohortCourse              | LearningAPI_cohortcou                 |
| Capstone                  | LearningAPI_capstone                  |
| CapstoneTimeline          | LearningAPI_capstonetimeline          |
| ProposalStatus            | LearningAPI_proposalstatus            |
| LearningObjective         | LearningAPI_learningobjective         |
| TaxonomyLevel             | LearningAPI_taxonomylevel             |
| ObjectiveTag              | LearningAPI_objectivetag              |
| LightningExercise         | LearningAPI_lightningexercise         |
| LightningTag              | LearningAPI_lightningtag              |
| FoundationsLearnerProfile | LearningAPI_foundationslearnerprofile |
| FoundationsExercise       | LearningAPI_foundationsexercise       |
| CoreSkill                 | LearningAPI_coreskill                 |
| CoreSkillRecord           | LearningAPI_coreskillrecord           |
| CoreSkillRecordEntry      | LearningAPI_coreskillrecordentry      |
| LearningWeight            | LearningAPI_learningweight            |
| LearningRecord            | LearningAPI_learningrecord            |
| LearningRecordEntry       | LearningAPI_learningrecordentry       |
| Tag                       | LearningAPI_tag                       |



| Property Name |  Column Name  |        Data Type         |
|---------------|---------------|--------------------------|
| id            | id            | integer (auto PK)        |
| user          | user_id       | integer (FK → auth_user) |
| slack_handle  | slack_handle  | varchar(55)              |
| github_handle | github_handle | varchar(55)              |


## 4. Relationship Examples

**One-to-one** (field name: OneToOneField)
- File: LearningAPI/models/people/nssuser.py:12
- Model: NssUser
- Field: user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
- Meaning: each NssUser extends exactly one Django auth User — a 1:1 link between the built-in user table and this app's extra profile data.
One-to-one (field name: cohort) — CohortInfo.cohort
One Cohort has exactly one CohortInfo record (extra details like Zoom URL, GitHub classroom link). Defined in LearningAPI/models/people/cohort_info.py:127.

**One-to-many** (field name: ForeignKey )
- File: LearningAPI/models/skill/core_skill_record.py:10
- Model: CoreSkillRecord
- Field: student = models.ForeignKey(NssUser, on_delete=models.CASCADE, related_name='core_skills')
- Meaning: one NssUser can have many CoreSkillRecord rows (their skill-level history), but each record points to a single student. related_name='core_skills' lets you go nssuser.core_skills.all().
One-to-many (field name: course) — Book.course
One Course contains many Books, but each Book belongs to exactly one Course. Defined in LearningAPI/models/coursework/book.py:723.

**Many-to-many** (field name: ManyToManyField )
- File: LearningAPI/models/people/student_team.py:9
- Model: StudentTeam
- Field: students = models.ManyToManyField("NSSUser", through="NSSUserTeam")
- Meaning: a team has many students and a student can belong to many teams. It uses through="NSSUserTeam" — an explicit join-table model rather than Django's auto-generated one, usually because that join table needs its own extra fields.

A student can be on many teams, and a team has many students. Django manages this through the explicit junction model NSSUserTeam. Defined in LearningAPI/models/people/student_team.py:355.

```mermaid
erDiagram

    %% ===== Django's built-in auth user (external, referenced by NssUser) =====
    User {
        string username
        string first_name
        string last_name
        string email
        boolean is_staff
    }

    %% ===== people app =====
    NssUser {
        int user_id FK
        string slack_handle
        string github_handle
    }

    Cohort {
        string name
        string slack_channel
        date start_date
        date end_date
        date break_start_date
        date break_end_date
        boolean active
    }

    CohortInfo {
        int cohort_id FK
        string student_organization_url
        string github_class
        string attendance_sheet_url
        string client_course_url
        string server_course_url
        string zoom_url
    }

    CohortEvent {
        int cohort_id FK
        string event_name
        int event_type_id F
        datetime event_datetime
        string description
        datetime created_at
        datetime updated_at
    }

    CohortEventType {
        string description
        string color
    }

    CohortGithubProject {
        int cohort_id FK
        string project_name
        boolean assessment
        string project_url
    }

    NssUserCohort {
        int nss_user_id FK
        int cohort_id FK
        boolean is_github_org_member
    }

    StudentTeam {
        string group_name
        int cohort_id FK
        boolean sprint_team
        string slack_channel
    }

    NSSUserTeam {
        int team_id FK
        int student_id FK
    }

    GroupProjectRepository {
        int team_id FK
        int project_id FK
        string repository
    }

    OneOnOneNote {
        int student_id FK
        int coach_id FK
        string notes
        datetime session_date
    }

    Opportunity {
        int senior_instructor_id FK
        int cohort_id FK
        string portion
        date start_date
        string message
    }

    OpportunityUser {
        int student_id FK
        int opportunity_id FK
        date date_created
    }

    Assessment {
        string name
        string source_url
        int book_id FK
        string type
    }

    AssessmentObjective {
        int assessment_id FK
        int objective_id FK
    }

    StudentAssessment {
        int student_id FK
        int assessment_id FK
        int status_id FK
        int instructor_id FK
        string url
        date date_created
    }

    StudentAssessmentStatus {
        string status
    }

    StudentMentor {
        int student_id FK
        int mentor_id FK
        int capstone_id FK
    }

    StudentNote {
        int student_id FK
        int coach_id FK
        int note_type_id FK
        string note
        datetime created_on
    }

    StudentNoteType {
        string label
    }

    StudentPersonality {
        int student_id FK
        string briggs_myers_type
        int bfi_extraversion
        int bfi_agreeableness
        int bfi_conscientiousness
        int bfi_neuroticism
        int bfi_openness
    }

    StudentTag {
        int student_id FK
        int tag_id FK
    }

    %% ===== coursework app =====
    Course {
        string name
        date date_created
        boolean active
    }

    Book {
        string name
        int course_id FK
        string description
        int index
    }

    Project {
        string name
        string implementati
        string client_template_url
        string api_template_url
        int book_id FK
        int index
        boolean active
        boolean is_group_project
    }

    ProjectNote {
        int user_id FK
        int project_id FK
        string note
    }

    ProjectTag {
        int project_id FK
        int tag_id FK
    }

    ProposalStatus {
        string status
    }

    Capstone {
        int student_id FK
        int course_id FK
        string proposal_url
        string repo_url
        string description
    }

    CapstoneTimeline {
        int capstone_id FK
        int status_id FK
        datetime date
    }

    CohortCourse {
        int cohort_id FK
        int course_id FK
        boolean active
        int index
    }

    StudentProject {
        int student_id FK
        int project_id FK
        date date_created
    }

    FoundationsExercise {
        string learner_github_id
        string learner_name
        string title
        string slug
        int attempts
        boolean complete
        datetime completed_on
        datetime first_attempt
        datetime last_attempt
        string completed_code
        boolean used_solution
    }

    FoundationsLearnerProfile {
        string learner_github_id
        string learner_name
        string cohort_type
        int cohort_number
        }

    LearningObjective {
        string swbat
        int bloom_level_id FK
    }

    LightningExercise {
        string name
        string description
    }

    LightningTag {
        int exercise_id FK
        int tag_id FK
    }

    ObjectiveTag {
        int objective_id FK
        int tag_id FK
    }

    TaxonomyLevel {
        string level_name
    }

    %% ===== skill app =====
    AssessmentWeight {
        int weight_id FK
        int assessment_id FK
    }

    CoreSkill {
        string label
    }

    CoreSkillRecord {
        int student_id FK
        int skill_id FK
        int level
        date created_on
    }

        CoreSkillRecordEntry {
        int record_id FK
        string note
        date recorded_on
        int instructor_id FK
    }

        LearningRecord {
        int student_id FK
        int weight_id FK
        boolean achieved
        date created_on
    }

    LearningRecordEntry {
        int record_id FK
        string note
        date recorded_on
        int instructor_id FK
    }

    LearningWeight {
        string label
        int weight
        int tier
    }

    %% ===== root =====
    Tag {
        string name
        }

    %% ================= RELATIONSHIPS =================

    %% -- OneToOneField --
    User ||--o| NssUser : user
    Cohort ||--o| CohortInfo : cohort
    NssUser ||--o| StudentPersonality : student

    %% -- coursework FKs --
    Course ||--o{ Book : course
    NssUser ||--o{ Capstone : student
    Course ||--o{ Capstone : course
    Capstone ||--o{ CapstoneTimeline : capstone
    ProposalStatus ||--o{ CapstoneTimeline : status
    Cohort ||--o{ CohortCou
    Course ||--o{ CohortCourse : course
    TaxonomyLevel ||--o{ Le
    LightningExercise ||--o{ LightningTag : exercise
    Tag ||--o{ LightningTag : tag
    LearningObjective ||--o{ ObjectiveTag : objective
    Tag ||--o{ ObjectiveTag
    Book ||--o{ Project : book
    NssUser ||--o{ ProjectNote : user
    Project ||--o{ ProjectNote : project
    Project ||--o{ ProjectTag : project
    Tag ||--o{ ProjectTag : tag
    NssUser ||--o{ StudentProject : student
    Project ||--o{ StudentProject : project

    %% -- people FKs --
    Book ||--o{ Assessment : book
    Assessment ||--o{ AssessmentObjective : assessment
    LearningObjective ||--o{ AssessmentObjective : objective
    Cohort ||--o{ CohortEvent : cohort
    CohortEventType ||--o{
    Cohort ||--o{ CohortGithubProject : cohort
    StudentTeam ||--o{ GroupProjectRepository : team
    Project ||--o{ GroupProjectRepository : project
    NssUser ||--o{ NssUserCohort : nss_user
    Cohort ||--o{ NssUserCohort : cohort
    StudentTeam ||--o{ NSSU
    NssUser ||--o{ NSSUserTeam : student
    NssUser ||--o{ OneOnOneNote : student
    NssUser ||--o{ OneOnOne    NssUser ||--o{ Opportun
    Cohort ||--o{ Opportunity : cohort
    NssUser ||--o{ OpportunityUser : student
    Opportunity ||--o{ OpportunityUser : opportunity
    NssUser ||--o{ StudentA
    Assessment ||--o{ StudentAssessment : assessment
    StudentAssessmentStatus ||--o{ StudentAssessment : status
    NssUser o|--o{ StudentAssessment : instructor
    NssUser ||--o{ StudentMentor : student
    NssUser ||--o{ StudentMentor : mentor
    Capstone ||--o{ StudentMentor : capstone
    NssUser ||--o{ StudentNote : student
    NssUser ||--o{ StudentNote : coach
    StudentNoteType o|--o{ StudentNote : note_type
    NssUser ||--o{ StudentTag : student
    Tag ||--o{ StudentTag : tag
    Cohort ||--o{ StudentTeam : cohort

    %% -- skill FKs --
    LearningWeight ||--o{ A    Assessment ||--o{ Asses
    NssUser ||--o{ CoreSkillRecord : student
    CoreSkill ||--o{ CoreSkillRecord : skill
    CoreSkillRecord ||--o{ CoreSkillRecordEntry : record
    NssUser ||--o{ CoreSkil
    NssUser ||--o{ LearningRecord : student
    LearningWeight ||--o{ LearningRecord : weight
    LearningRecord ||--o{ LearningRecordEntry : record
    NssUser ||--o{ LearningRecordEntry : instructor

How to read the notation: || = exactly one, o| = zero-or-one (nullable FK), o{ = zero-or-many.
