# Secure Architecture & Threat Modeling Report  
## University Management System (UMS)

---

# 1. System Overview

## 1.1 Purpose

The University Management System (UMS) is an internet-facing enterprise platform that supports:

- Student access: LMS, Attendance, Fees, Grades, Alumni, Wellness, Library Management
- Faculty access: LMS, Attendance, Salary information, Course management, Library, Wellness
- Administrative / Registrar functions: Student records, Academic management, Financial operations, Role provisioning
- External integrations: Payment providers, Job portal feeds, Email/SMS gateways, Identity provider (SSO)

The system is cloud-agnostic and accessible over the public internet. Both external attackers and insider threats are considered.

---

## 1.2 Assumptions

- System is accessible over the public internet.
- Users authenticate via centralized Identity Provider (IdP).
- System stores sensitive personal and financial data.
- Administrative users have high-impact privileges.
- Third-party integrations are untrusted by default.
- Secure development lifecycle is partially implemented but not assumed perfect.
- The deployment environment follows standard enterprise practices.
- Security must be implemented at architectural level, not only at code level.

---

# 2. System definition + architecture 

## 2.1 Components (what exists)

### User-facing

- Student Portal (LMS, Attendance, Fees, Grades, Alumni, Wellness, Library)
- Faculty Portal (LMS, Attendance, Salary/HR view, Courses, Library, Wellness)
- Admin/Registrar Portal (high privilege: student records, fees, grades, provisioning)

### Backend

- API Backend (core business logic)
- Auth Service / Identity Provider (IdP) integration (SSO)
- Notification service (email/SMS)
- Reporting/Analytics service

### Data stores

- Student DB (PII + academic records)
- Faculty/HR DB (salaries, contracts)
- Finance DB (fees, invoices, payment status)
- LMS content store (files, submissions)
- Logs/Audit store

### External integrations

- Payment provider / bank rails (fees)
- Job portals / company job feeds
- Email/SMS gateway
  
---

## 2.2 Users & roles (actors)

- Student (internet-facing)
- Faculty (internet-facing)
- Admin/Registrar (internet-facing but separate admin plane)
- IT Ops / DBA (insiders)
- Third parties (payment provider, job portals)
- External attacker (anonymous internet)

---

## 2.3 Trust Boundaries

The following trust boundaries are defined:

- **TB1 – Internet Boundary**  
  Between external users and the university edge infrastructure.
  Untrusted traffic enters (students/faculty/admin)

- **TB2 – Application Boundary**  
  Between edge infrastructure and backend services.
  Only controlled service-to-service access

- **TB3 – Data Boundary**  
  Between application services and databases.
  Strictly segmented, least privilege

- **TB4 – Third-Party Boundary**  
  Between university services and external providers.
  Payment + job portal integrations (untrusted inputs)

---

## 2.4 Initial Architecture Diagram

![Initial Architecture](./diagrams/architecture-v1.png)

The architecture consists of:

- Web frontend
- Admin frontend
- API backend
- LMS, Fees, Grades, Library, Wellness services
- Student, HR, and Finance databases
- Logging system
- Third-party integrations

Administrative traffic is routed separately from student/faculty traffic.

---

# 3. Asset Identification & Security Objectives

Full asset list available in:

- [Asset Inventory (CSV)](./tables/asset-inventory.csv)

## 3.1 Critical Assets

- Credentials & authentication tokens
- Student personally identifiable information (PII)
- Academic records (grades, transcripts)
- Financial data (fees, invoices)
- Faculty salary information
- LMS files and submissions
- Administrative privileges
- Audit logs
- API secrets and keys

---

## 3.2 Security Objectives

### Confidentiality
Prevent unauthorized access to personal, financial, and administrative data.

### Integrity
Ensure grades, attendance records, fees, and logs cannot be tampered with.

### Availability
Ensure uptime during peak academic periods such as exams and registration.

### Accountability
All sensitive actions must be traceable to uniquely authenticated users.

---
