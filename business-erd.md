# 09. Business ERD

## Purpose

This document defines the core business entities of the Tutorflix platform.

The Business Layer represents the operational workflow of the academy, including lead management, trial classes, student enrollment, tutor assignments, scheduling, lesson packages, and payment processing.

---

# Entity Relationship Diagram

```mermaid
erDiagram

%% =========================
%% LEADS
%% =========================

LEADS {

uuid id PK

string full_name

string email

string phone

string curriculum

string grade

string status

uuid assigned_to FK

datetime created_at

}

LEAD_NOTES {

uuid id PK

uuid lead_id FK

uuid created_by FK

text note

datetime created_at

}

LEAD_ACTIVITIES {

uuid id PK

uuid lead_id FK

string activity_type

uuid performed_by FK

datetime created_at

}

%% =========================
%% TRIALS
%% =========================

TRIALS {

uuid id PK

uuid lead_id FK

uuid tutor_id FK

datetime scheduled_at

string status

string outcome

}

TRIAL_FEEDBACK {

uuid id PK

uuid trial_id FK

uuid tutor_id FK

text feedback

}

%% =========================
%% STUDENTS
%% =========================

STUDENTS {

uuid id PK

uuid user_id FK

string curriculum

string grade

string status

}

%% =========================
%% PARENTS
%% =========================

PARENTS {

uuid id PK

uuid user_id FK

}

STUDENT_PARENTS {

uuid student_id FK

uuid parent_id FK

}

%% =========================
%% TUTORS
%% =========================

TUTORS {

uuid id PK

uuid user_id FK

string bio

string qualification

string status

}

TUTOR_AVAILABILITY {

uuid id PK

uuid tutor_id FK

int weekday

time start_time

time end_time

}

%% =========================
%% TUTOR ASSIGNMENTS
%% =========================

TUTOR_ASSIGNMENTS {

uuid id PK

uuid tutor_id FK

uuid student_id FK

boolean primary_tutor

string status

datetime assigned_at

}

%% =========================
%% SUBJECTS
%% =========================

SUBJECTS {

uuid id PK

string name

}

STUDENT_SUBJECTS {

uuid student_id FK

uuid subject_id FK

}

TUTOR_SUBJECTS {

uuid tutor_id FK

uuid subject_id FK

}

%% =========================
%% CLASS REQUESTS
%% =========================

CLASS_REQUESTS {

uuid id PK

uuid student_id FK

uuid tutor_id FK

datetime requested_date

string requested_by

string status

}

%% =========================
%% CLASSES
%% =========================

CLASSES {

uuid id PK

uuid tutor_assignment_id FK

uuid subject_id FK

datetime start_time

datetime end_time

string status

}

ATTENDANCE {

uuid id PK

uuid class_id FK

uuid student_id FK

string attendance_status

}

%% =========================
%% PACKAGES
%% =========================

PACKAGES {

uuid id PK

uuid student_id FK

string package_name

int total_hours

int remaining_hours

string status

}

PACKAGE_TRANSACTIONS {

uuid id PK

uuid package_id FK

int hours

string transaction_type

datetime created_at

}

%% =========================
%% PAYMENTS
%% =========================

PAYMENTS {

uuid id PK

uuid package_id FK

decimal amount

string provider

string status

datetime paid_at

}

%% =========================
%% RELATIONSHIPS
%% =========================

LEADS ||--o{ LEAD_NOTES : has

LEADS ||--o{ LEAD_ACTIVITIES : has

LEADS ||--o| TRIALS : books

TRIALS ||--|| TRIAL_FEEDBACK : receives

TRIALS ||--o| STUDENTS : converts_to

STUDENTS ||--o{ STUDENT_PARENTS : linked

PARENTS ||--o{ STUDENT_PARENTS : linked

TUTORS ||--o{ TUTOR_AVAILABILITY : defines

STUDENTS ||--o{ TUTOR_ASSIGNMENTS : assigned

TUTORS ||--o{ TUTOR_ASSIGNMENTS : teaches

STUDENTS ||--o{ STUDENT_SUBJECTS : studies

SUBJECTS ||--o{ STUDENT_SUBJECTS : includes

TUTORS ||--o{ TUTOR_SUBJECTS : teaches

SUBJECTS ||--o{ TUTOR_SUBJECTS : includes

STUDENTS ||--o{ CLASS_REQUESTS : requests

TUTORS ||--o{ CLASS_REQUESTS : approves

TUTOR_ASSIGNMENTS ||--o{ CLASSES : schedules

SUBJECTS ||--o{ CLASSES : belongs_to

CLASSES ||--o{ ATTENDANCE : records

STUDENTS ||--o{ PACKAGES : owns

PACKAGES ||--o{ PACKAGE_TRANSACTIONS : tracks

PACKAGES ||--o{ PAYMENTS : paid_by
```

---

# Business Workflow

```mermaid
flowchart LR

Lead

-->

Trial

-->

Student

-->

Tutor Assignment

-->

Package

-->

Payment

-->

Class Request

-->

Class

-->

Attendance
```

---

# Entity Responsibilities

| Entity | Responsibility |
|---------|----------------|
| Leads | Prospective students |
| Lead Notes | Follow-up notes |
| Lead Activities | Lead history |
| Trials | Introductory lessons |
| Trial Feedback | Tutor evaluation of the trial |
| Students | Enrolled learners |
| Parents | Parent or guardian records |
| Student Parents | Student–parent relationship |
| Tutors | Tutor profiles |
| Tutor Availability | Weekly availability schedule |
| Tutor Assignments | Student–tutor assignments and history |
| Subjects | Subjects offered by the academy |
| Student Subjects | Subjects studied by students |
| Tutor Subjects | Subjects taught by tutors |
| Class Requests | Requested lesson schedule |
| Classes | Confirmed lessons |
| Attendance | Attendance tracking |
| Packages | Purchased lesson packages |
| Package Transactions | Package hour adjustments |
| Payments | Payment records |

---

# Design Decisions

- Leads are converted into Students after successful trials.
- Tutors are assigned through the Tutor Assignments entity rather than directly to students.
- Scheduling is separated into Class Requests and Classes.
- Parents and Students use a many-to-many relationship.
- Subjects are shared between students and tutors.
- Payments are linked to Packages rather than directly to Students.
- Package balances are maintained through Package Transactions.
- Tutor availability is managed separately from scheduled classes.
