# 09. Business ERD

## Purpose

This document defines the core business entities of the Tutorflix platform.

The Business Layer models the complete operational workflow of the academy, including lead management, trial lessons, student enrollment, tutor assignments, scheduling, lesson packages, attendance, and payment processing.

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

datetime ended_at

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

datetime requested_start

datetime requested_end

string requested_by

string status

}

%% =========================
%% CLASSES (Series)
%% =========================

CLASSES {

uuid id PK

uuid tutor_assignment_id FK

uuid subject_id FK

string title

string recurrence

date start_date

date end_date

string status

}

%% =========================
%% CLASS SESSIONS
%% =========================

CLASS_SESSIONS {

uuid id PK

uuid class_id FK

datetime start_time

datetime end_time

string meeting_link

string status

}

ATTENDANCE {

uuid id PK

uuid session_id FK

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

int purchased_hours

string status

date expires_at

}

PACKAGE_TRANSACTIONS {

uuid id PK

uuid package_id FK

int hours

string transaction_type

string reference_type

uuid reference_id

datetime created_at

}

%% =========================
%% PAYMENTS
%% =========================

PAYMENTS {

uuid id PK

uuid package_id FK

decimal amount

string payment_method

string payment_provider

string status

string receipt_url

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

TUTOR_ASSIGNMENTS ||--o{ CLASSES : owns

CLASSES ||--o{ CLASS_SESSIONS : contains

CLASS_SESSIONS ||--o{ ATTENDANCE : records

SUBJECTS ||--o{ CLASSES : teaches

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

Package Purchase

-->

Payment

-->

Class Request

-->

Class

-->

Class Session

-->

Attendance

-->

Package Transaction
```

---

# Package Balance

Package hours are **not stored directly**.

The available balance is calculated from `PACKAGE_TRANSACTIONS`.

Example:

| Transaction | Hours |
|-------------|------:|
| Package Purchase | +20 |
| Class Completed | -1 |
| Makeup Credit | +1 |
| Refund | -2 |

Current Balance = **18 Hours**

This approach ensures a complete audit trail and prevents inconsistencies.

---

# Payment Model

The payment system is provider-independent.

## Current Provider

- Manual Verification

## Future Providers

- Stripe
- PayPal
- Local Payment Gateways

### Payment Method

Examples:

- Manual
- Credit Card
- Bank Transfer
- Wallet

### Payment Provider

Examples:

- Manual
- Stripe
- PayPal

This allows new payment gateways to be introduced without changing the business model.

---

# Scheduling Model

Scheduling is divided into three stages:

1. **Class Request** – A student, parent, tutor, or admin requests a lesson.
2. **Class** – Represents the recurring lesson or course.
3. **Class Session** – Represents each individual lesson occurrence.

This separation supports recurring schedules, cancellations, rescheduling, attendance, and makeup lessons without duplicating class information.

---

# Design Decisions

- Leads are converted into Students after successful trials.
- Tutors are assigned through Tutor Assignments.
- Scheduling separates requests, recurring classes, and individual sessions.
- Parents and Students have a many-to-many relationship.
- Subjects are linked independently to students and tutors.
- Package balances are derived from Package Transactions.
- Payments use a provider-independent architecture.
- Tutor availability is independent of scheduled classes.
- Individual attendance is tracked per Class Session.
