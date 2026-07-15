# 11. System ERD

## Purpose

This document defines the platform-wide operational entities that support all business domains.

Unlike the Business and Communication layers, these entities are shared across the entire application and are not owned by a single module.

---

# Entity Relationship Diagram

```mermaid
erDiagram

%% ==========================================
%% NOTIFICATIONS
%% ==========================================

NOTIFICATIONS {

uuid id PK

uuid user_id FK

string type

string title

text body

string source_module

uuid reference_id

boolean is_read

datetime created_at

datetime read_at

}

%% ==========================================
%% AUDIT LOGS
%% ==========================================

AUDIT_LOGS {

uuid id PK

uuid user_id FK

string module

string action

uuid entity_id

json old_values

json new_values

datetime created_at

}

%% ==========================================
%% FILES
%% ==========================================

FILES {

uuid id PK

string bucket

string path

string file_name

string mime_type

int file_size

uuid uploaded_by FK

datetime uploaded_at

}

%% ==========================================
%% SETTINGS
%% ==========================================

SETTINGS {

uuid id PK

string key

json value

string category

}

%% ==========================================
%% FEATURE FLAGS
%% ==========================================

FEATURE_FLAGS {

uuid id PK

string name

boolean enabled

string description

}

%% ==========================================
%% RELATIONSHIPS
%% ==========================================

FILES ||--o{ AUDIT_LOGS : generates
```

---

# Notification Sources

Notifications may originate from any module.

```mermaid
flowchart TD

Leads

Trials

Scheduling

Payments

Communication

Administration

Leads --> Notifications

Trials --> Notifications

Scheduling --> Notifications

Payments --> Notifications

Communication --> Notifications

Administration --> Notifications
```

---

# Notification Examples

| Module | Event |
|----------|------------------------------|
| Communication | New message |
| Trials | Trial scheduled |
| Scheduling | Class reminder |
| Scheduling | Class cancelled |
| Payments | Payment approved |
| Payments | Payment rejected |
| Packages | Low remaining hours |
| Administration | New announcement |
| Tutors | Tutor assigned |
| Students | Welcome notification |

---

# Audit Logs

Audit Logs provide a permanent history of important business events.

Examples

- Lead converted
- Student created
- Tutor assigned
- Package updated
- Payment verified
- Message deleted
- User promoted
- Permission changed

Audit records are immutable and are never deleted.

---

# Files

Binary files are stored in Supabase Storage.

The Files table stores metadata only.

Examples

- Profile pictures
- Payment receipts
- Homework
- Assignments
- Chat attachments

---

# Settings

Stores configurable system settings.

Examples

- Academy working hours
- Trial duration
- Default lesson duration
- Time zone
- Chat moderation rules

---

# Feature Flags

Controls optional functionality without code changes.

Examples

- AI Moderation
- Stripe Payments
- PayPal Integration
- WhatsApp Notifications
- Parent Portal Beta

---

# Design Decisions

- Notifications are a shared platform service.
- Audit Logs record all important system actions.
- Files are stored in Supabase Storage with metadata stored in PostgreSQL.
- Settings are configurable without code changes.
- Feature Flags enable gradual rollout of new functionality.
