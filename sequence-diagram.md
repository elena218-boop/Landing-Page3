# 18. Sequence Diagrams

## Purpose

This document describes the sequence of interactions between users, frontend applications, backend services, and infrastructure for the core business processes of the Tutorflix platform.

---

# Table of Contents

1. User Authentication
2. Lead Creation
3. Trial Scheduling
4. Lead Conversion
5. Student Package Purchase
6. Class Request
7. Class Scheduling
8. Join Class
9. Attendance Recording
10. Package Hour Deduction
11. Chat Messaging
12. Chat Moderation
13. Manual Payment Verification
14. Tutor Assignment
15. Notification Flow

---
## User Authentication

```mermaid
sequenceDiagram

actor User

participant Frontend

participant Supabase

participant Backend

User->>Frontend: Login

Frontend->>Supabase: Authenticate

Supabase-->>Frontend: JWT

Frontend->>Backend: API Request + JWT

Backend->>Supabase: Verify Token

Supabase-->>Backend: Valid

Backend-->>Frontend: User Data

Frontend-->>User: Dashboard
```
## Lead Creation

```mermaid
sequenceDiagram

actor Sales

participant Portal

participant API

participant Database

Sales->>Portal: Create Lead

Portal->>API: POST /leads

API->>Database: Save Lead

Database-->>API: Success

API-->>Portal: Lead Created
```
## Trial Scheduling

```mermaid
sequenceDiagram

actor IntroScheduler

participant Portal

participant API

participant Database

participant Notification

IntroScheduler->>Portal: Schedule Trial

Portal->>API: Schedule Trial

API->>Database: Save Trial

API->>Notification: Notify Tutor & Student

Notification-->>Portal: Notification Created

API-->>Portal: Success
```
## Lead Conversion

```mermaid
sequenceDiagram

actor Admin

participant Portal

participant API

participant Database

Admin->>Portal: Convert Lead

Portal->>API: Convert

API->>Database: Create Student

API->>Database: Update Lead Status

Database-->>API: Success

API-->>Portal: Student Created
```
## Package Purchase

```mermaid
sequenceDiagram

actor Admin

participant Portal

participant API

participant Database

Admin->>Portal: Assign Package

Portal->>API: Create Package

API->>Database: Package

API->>Database: Package Transaction (+Hours)

Database-->>API: Success

API-->>Portal: Package Created
```
## Manual Payment Verification

```mermaid
sequenceDiagram

actor Student

actor Finance

participant Portal

participant API

participant Storage

participant Database

Student->>Portal: Upload Receipt

Portal->>Storage: Upload File

Storage-->>Portal: File URL

Portal->>API: Payment Request

API->>Database: Pending Payment

Finance->>Portal: Verify Payment

Portal->>API: Approve Payment

API->>Database: Payment Approved

API->>Database: Activate Package

API-->>Portal: Success
```
## Tutor Assignment

```mermaid
sequenceDiagram

actor Admin

participant Portal

participant API

participant Database

Admin->>Portal: Assign Tutor

Portal->>API: Assignment Request

API->>Database: Create Assignment

API->>Database: Create Conversation

Database-->>API: Success

API-->>Portal: Assignment Completed
```
## Class Request

```mermaid
sequenceDiagram

actor Student

participant Portal

participant API

participant Database

Student->>Portal: Request Class

Portal->>API: Create Request

API->>Database: Save Request

Database-->>API: Success

API-->>Portal: Request Submitted
```
## Class Scheduling

```mermaid
sequenceDiagram

actor Admin

participant Portal

participant API

participant Database

Admin->>Portal: Approve Request

Portal->>API: Schedule Class

API->>Database: Create Class

API->>Database: Create Sessions

Database-->>API: Success

API-->>Portal: Schedule Ready
```
## Join Class

```mermaid
sequenceDiagram

actor Student

participant Portal

participant API

participant Database

Student->>Portal: Join Class

Portal->>API: Validate Session

API->>Database: Fetch Session

Database-->>API: Meeting Link

API-->>Portal: Join Details
```
## Attendance Recording

```mermaid
sequenceDiagram

actor Tutor

participant Portal

participant API

participant Database

Tutor->>Portal: Mark Attendance

Portal->>API: Attendance

API->>Database: Save Attendance

Database-->>API: Success

API-->>Portal: Attendance Saved
```
## Package Hour Deduction

```mermaid
sequenceDiagram

actor System

participant API

participant Database

API->>Database: Attendance Verified

API->>Database: Package Transaction (-1 Hour)

Database-->>API: Updated Balance
```
## Chat Messaging

```mermaid
sequenceDiagram

actor User

participant Portal

participant API

participant Database

participant Realtime

User->>Portal: Send Message

Portal->>API: Message

API->>Database: Save Message

API->>Realtime: Broadcast

Realtime-->>Portal: New Message
```
## Chat Moderation

```mermaid
sequenceDiagram

actor User

participant API

participant Moderation

participant Database

User->>API: Message

API->>Moderation: Validate

Moderation->>Moderation: Phone Detection

Moderation->>Moderation: Email Detection

Moderation->>Moderation: URL Detection

Moderation->>Moderation: Profanity Detection

alt Allowed

Moderation-->>API: Approved

API->>Database: Save Message

else Blocked

Moderation-->>API: Rejected

API-->>User: Validation Error

end
```
## Notification Flow

```mermaid
sequenceDiagram

participant Module

participant NotificationService

participant Database

participant Portal

Module->>NotificationService: Create Notification

NotificationService->>Database: Save

Database-->>NotificationService: Success

NotificationService-->>Portal: Realtime Notification
```
Class Request
        │
        ▼
Approved
        │
        ▼
Recurring Class Created
        │
        ▼
Weekly Session Generated
        │
        ▼
Tutor Starts Session
        │
        ▼
Student Joins
        │
        ▼
Attendance Recorded
        │
        ▼
Package Hour Deducted
        │
        ▼
Notification Sent
