# Software Requirement Specification (SRS)

## Bengkel Koding Web V2

**Document Version**: 1.0  
**Date**: November 2025  
**Status**: Approved  
**Project**: Bengkel Koding Web V2  
**Organization**: Universitas Dian Nuswantoro

---

## Document Control

| Version | Date     | Author   | Changes         |
| ------- | -------- | -------- | --------------- |
| 1.0     | Nov 2025 | Dev Team | Initial version |

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Overall Description](#2-overall-description)
3. [System Features](#3-system-features)
4. [External Interface Requirements](#4-external-interface-requirements)
5. [Non-Functional Requirements](#5-non-functional-requirements)
6. [Appendices](#6-appendices)

---

## 1. Introduction

### 1.1 Purpose

Dokumen Software Requirement Specification (SRS) ini menjelaskan secara detail kebutuhan sistem untuk **Bengkel Koding Web V2**, sebuah platform pembelajaran pemrograman berbasis web untuk Universitas Dian Nuswantoro Semarang.

Dokumen ini ditujukan untuk:

- Development team untuk implementation
- Quality assurance team untuk testing
- Project stakeholders untuk review
- System administrators untuk deployment
- End users untuk understanding sistem capabilities

### 1.2 Scope

**Bengkel Koding Web V2** adalah comprehensive learning management system yang menyediakan:

**In Scope**:

- ✅ Multi-role user management (student, asisten, dosen, admin, superadmin)
- ✅ Course dan class management
- ✅ Learning path dengan token-based access
- ✅ Assignment dan exam system
- ✅ Digital attendance dengan QR code
- ✅ Progress tracking dan analytics
- ✅ Digital certification
- ✅ Responsive web interface

**Out of Scope**:

- ❌ Mobile native applications (iOS/Android)
- ❌ Live video streaming
- ❌ Payment processing
- ❌ Forum/discussion board (phase 2)
- ❌ AI-powered features (phase 2)

### 1.3 Definitions, Acronyms, and Abbreviations

| Term       | Definition                         |
| ---------- | ---------------------------------- |
| **LMS**    | Learning Management System         |
| **JWT**    | JSON Web Token                     |
| **RBAC**   | Role-Based Access Control          |
| **QR**     | Quick Response (code)              |
| **API**    | Application Programming Interface  |
| **SSR**    | Server-Side Rendering              |
| **CSR**    | Client-Side Rendering              |
| **CRUD**   | Create, Read, Update, Delete       |
| **SRS**    | Software Requirement Specification |
| **UDINUS** | Universitas Dian Nuswantoro        |

### 1.4 References

- Next.js 14 Documentation: https://nextjs.org/docs
- TypeScript Documentation: https://www.typescriptlang.org/docs
- Tailwind CSS Documentation: https://tailwindcss.com/docs
- React Documentation: https://react.dev

### 1.5 Overview

Dokumen ini terorganisir sebagai berikut:

- **Section 2**: Overall system description
- **Section 3**: Detailed functional requirements
- **Section 4**: External interface requirements
- **Section 5**: Non-functional requirements
- **Section 6**: Appendices dan additional information

---

## 2. Overall Description

### 2.1 Product Perspective

Bengkel Koding Web V2 adalah standalone web application yang berkomunikasi dengan backend API untuk data operations. System architecture:

```
┌─────────────────────────────────────────┐
│         Web Browser (Client)            │
│  ┌───────────────────────────────────┐  │
│  │   Next.js Application             │  │
│  │  - React Components               │  │
│  │  - Client State (Jotai)           │  │
│  │  - UI (Tailwind CSS)              │  │
│  └───────────────┬───────────────────┘  │
└──────────────────┼──────────────────────┘
                   │ HTTPS/REST
┌──────────────────┼──────────────────────┐
│  ┌───────────────▼───────────────────┐  │
│  │   Backend API Server              │  │
│  │  - RESTful Endpoints              │  │
│  │  - Authentication (JWT)           │  │
│  │  - Business Logic                 │  │
│  └───────────────┬───────────────────┘  │
│                  │                       │
│  ┌───────────────▼───────────────────┐  │
│  │   Database (PostgreSQL/MySQL)     │  │
│  │  - User Data                      │  │
│  │  - Course Content                 │  │
│  │  - Learning Records               │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

### 2.2 Product Functions

Major functions provided:

1. **User Management**

   - Registration dan authentication
   - Profile management
   - Role-based access control

2. **Course Management**

   - Browse dan search courses
   - Course detail information
   - Enrollment process

3. **Learning Content Delivery**

   - Session-based learning materials
   - Rich text content (Markdown)
   - Downloadable resources

4. **Assessment System**

   - Assignment creation dan submission
   - Online examinations
   - Grading dan feedback

5. **Progress Tracking**

   - Learning progress monitoring
   - Performance analytics
   - Activity logs

6. **Attendance Management**

   - QR code-based check-in
   - Attendance history
   - Reports

7. **Certification**

   - Certificate generation
   - Online verification

8. **Learning Paths**
   - Structured learning journeys
   - Token-based access
   - Progress across multiple courses

### 2.3 User Classes and Characteristics

#### Student

- **Primary users**: Mahasiswa UDINUS
- **Technical Expertise**: Basic computer dan web literacy
- **Frequency**: Daily during active courses
- **Key Features**: Browse courses, submit assignments, track progress

#### Asisten (Teaching Assistant)

- **Users**: Senior students atau junior staff
- **Technical Expertise**: Above average, familiar dengan teaching tools
- **Frequency**: Multiple times per week
- **Key Features**: Grade submissions, manage attendance, assist students

#### Dosen (Lecturer)

- **Users**: Faculty members
- **Technical Expertise**: Moderate to advanced
- **Frequency**: Daily during teaching periods
- **Key Features**: Create courses, manage classes, evaluate students

#### Admin

- **Users**: Platform administrators
- **Technical Expertise**: Advanced
- **Frequency**: Daily
- **Key Features**: Content management, user management, analytics

#### Super Admin

- **Users**: System administrators
- **Technical Expertise**: Expert
- **Frequency**: As needed
- **Key Features**: Full system access, configuration, monitoring

### 2.4 Operating Environment

**Client Requirements**:

- **Browser**: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **Screen Resolution**: Minimum 375px width (mobile) to 1920px+ (desktop)
- **Internet**: Minimum 1 Mbps connection
- **JavaScript**: Must be enabled

**Server Requirements**:

- **OS**: Linux (Ubuntu 20.04+ recommended) atau Windows Server
- **Runtime**: Node.js 18+ atau Bun
- **Memory**: Minimum 2GB RAM (4GB+ recommended)
- **Storage**: Minimum 20GB SSD

### 2.5 Design and Implementation Constraints

**Technical Constraints**:

- Must use Next.js 14 dengan App Router
- Must use TypeScript untuk type safety
- Frontend styling limited to Tailwind CSS
- Backend communication via RESTful API only

**Business Constraints**:

- Must comply dengan UDINUS IT policies
- Budget limitations untuk infrastructure
- Timeline: MVP dalam 6 bulan

**Regulatory Constraints**:

- Data protection compliance
- Educational content copyright
- User privacy regulations

### 2.6 Assumptions and Dependencies

**Assumptions**:

- Users have stable internet connection
- Backend API is available 99.5% of time
- Users have modern web browsers
- Database maintains data integrity

**Dependencies**:

- Backend API server availability
- Third-party services:
  - Google OAuth
  - reCAPTCHA
  - Email service provider
  - File storage service

---

## 3. System Features

### 3.1 User Authentication & Authorization

**Priority**: High  
**Description**: System untuk user login, registration, dan access control

#### 3.1.1 Registration

**REQ-AUTH-001**: Email Registration  
**Description**: Users dapat register menggunakan email dan password  
**Inputs**: Email, password, name, NIM/NIDN  
**Processing**: Validate input, hash password, store user, send verification email  
**Outputs**: Success message, verification email sent  
**Error Handling**: Invalid email format, duplicate email, weak password

**REQ-AUTH-002**: Google OAuth Registration  
**Description**: Users dapat register menggunakan Google account  
**Inputs**: Google OAuth token  
**Processing**: Validate token, create user account, auto-verify email  
**Outputs**: User logged in, redirected to dashboard

#### 3.1.2 Login

**REQ-AUTH-003**: Email/Password Login  
**Description**: Users login dengan credentials  
**Inputs**: Email, password  
**Processing**: Validate credentials, generate JWT, set cookies  
**Outputs**: JWT token, user role, redirect to appropriate dashboard  
**Error Handling**: Invalid credentials, unverified email, account locked

**REQ-AUTH-004**: Google OAuth Login  
**Description**: Users login via Google  
**Inputs**: Google OAuth token  
**Processing**: Validate token, generate JWT  
**Outputs**: JWT token, redirect to dashboard

#### 3.1.3 Authorization

**REQ-AUTH-005**: Role-Based Access Control  
**Description**: System enforce permissions berdasarkan user role  
**Inputs**: User role, requested route  
**Processing**: Check role permissions, validate access  
**Outputs**: Allow/deny access  
**Error Handling**: Redirect ke /unauthorized untuk invalid access

---

### 3.2 Course Management

**Priority**: High  
**Description**: Functionality untuk browse, view, dan enroll courses

#### 3.2.1 Course Catalog

**REQ-COURSE-001**: Browse Courses  
**Description**: Display list of available courses  
**Inputs**: Optional filters (category, level, search query)  
**Processing**: Fetch published courses, apply filters, sort  
**Outputs**: Paginated list of courses dengan metadata  
**Performance**: Load < 2 seconds

**REQ-COURSE-002**: Course Detail  
**Description**: Display detailed course information  
**Inputs**: Course ID atau slug  
**Processing**: Fetch course data, instructor info, reviews  
**Outputs**: Course detail page dengan all information  
**Error Handling**: 404 jika course tidak ditemukan

#### 3.2.2 Enrollment

**REQ-COURSE-003**: Course Enrollment  
**Description**: Student dapat enroll dalam course  
**Inputs**: Course ID, user ID  
**Processing**: Check capacity, create enrollment record  
**Outputs**: Enrollment confirmation, access to course content  
**Error Handling**: Class full, already enrolled, prerequisites not met

**REQ-COURSE-004**: Token-Based Enrollment  
**Description**: Enroll menggunakan access token  
**Inputs**: Token code  
**Processing**: Validate token, check usage limit, create enrollment  
**Outputs**: Enrollment confirmation  
**Error Handling**: Invalid token, expired token, usage limit exceeded

---

### 3.3 Class & Session Management

**Priority**: High  
**Description**: Manage classes dan learning sessions

#### 3.3.1 Class Management (Dosen)

**REQ-CLASS-001**: Create Class  
**Description**: Dosen dapat create class instance  
**Inputs**: Class name, course, dates, max students  
**Processing**: Validate dates, create class record  
**Outputs**: Class created, class ID  
**Authorization**: Dosen, Admin, SuperAdmin

**REQ-CLASS-002**: Assign Assistant  
**Description**: Dosen assign asisten to class  
**Inputs**: Class ID, assistant user ID  
**Processing**: Validate assistant role, create assignment  
**Outputs**: Assistant assigned confirmation

#### 3.3.2 Session Management

**REQ-SESSION-001**: Create Session  
**Description**: Create learning session untuk class  
**Inputs**: Title, number, date, content (Markdown)  
**Processing**: Validate session number uniqueness, store content  
**Outputs**: Session created, session ID

**REQ-SESSION-002**: Access Session Content  
**Description**: Student view session materials  
**Inputs**: Session ID, user ID  
**Processing**: Check enrollment, fetch content, render Markdown  
**Outputs**: Session content displayed  
**Authorization**: Enrolled students only

---

### 3.4 Assignment System

**Priority**: High  
**Description**: Assignment creation, submission, dan grading

#### 3.4.1 Assignment Creation

**REQ-ASSIGN-001**: Create Assignment  
**Description**: Dosen create assignment  
**Inputs**: Title, description, deadline, max score, type  
**Processing**: Validate inputs, store assignment  
**Outputs**: Assignment created  
**Authorization**: Dosen, Admin

#### 3.4.2 Submission

**REQ-ASSIGN-002**: Submit Assignment  
**Description**: Student submit assignment work  
**Inputs**: Assignment ID, content, files  
**Processing**: Validate deadline, store submission, upload files  
**Outputs**: Submission confirmation  
**Error Handling**: Late submission warning, file size limit

**REQ-ASSIGN-003**: Edit Submission  
**Description**: Student edit submission sebelum graded  
**Inputs**: Submission ID, updated content  
**Processing**: Check grading status, update submission  
**Outputs**: Submission updated  
**Error Handling**: Cannot edit after grading

#### 3.4.3 Grading

**REQ-ASSIGN-004**: Grade Submission  
**Description**: Dosen/Asisten grade student work  
**Inputs**: Submission ID, score, feedback  
**Processing**: Validate score range, update submission  
**Outputs**: Grade recorded, notification sent to student  
**Authorization**: Dosen, Asisten (for their classes)

---

### 3.5 Examination System

**Priority**: Medium  
**Description**: Online exam functionality

#### 3.5.1 Exam Creation

**REQ-EXAM-001**: Create Exam  
**Description**: Dosen create online exam  
**Inputs**: Title, duration, questions, start/end time  
**Processing**: Store exam data, validate times  
**Outputs**: Exam created, exam ID

#### 3.5.2 Taking Exam

**REQ-EXAM-002**: Start Exam  
**Description**: Student begin exam  
**Inputs**: Exam ID, user ID  
**Processing**: Check time window, create exam attempt  
**Outputs**: Exam interface, timer started  
**Error Handling**: Outside time window, already taken

**REQ-EXAM-003**: Submit Exam  
**Description**: Student submit exam answers  
**Inputs**: Exam ID, answers  
**Processing**: Validate time limit, calculate score, store results  
**Outputs**: Exam result, score  
**Error Handling**: Time expired auto-submit

---

### 3.6 Attendance System

**Priority**: Medium  
**Description**: Digital attendance dengan QR code

#### 3.6.1 QR Code Generation

**REQ-ATTEND-001**: Generate QR Code  
**Description**: Dosen/Asisten generate QR for session  
**Inputs**: Session ID  
**Processing**: Generate unique QR code, set expiry  
**Outputs**: QR code displayed  
**Authorization**: Dosen, Asisten

#### 3.6.2 Check-in

**REQ-ATTEND-002**: Scan QR Code  
**Description**: Student scan QR to mark attendance  
**Inputs**: QR code data, user ID  
**Processing**: Validate QR, check time window, create attendance record  
**Outputs**: Attendance marked, confirmation  
**Error Handling**: Invalid QR, expired QR, already checked in

**REQ-ATTEND-003**: Manual Attendance  
**Description**: Dosen/Asisten manually mark attendance  
**Inputs**: Session ID, student ID, status  
**Processing**: Create/update attendance record  
**Outputs**: Attendance updated  
**Use Case**: Makeup for technical issues

---

### 3.7 Progress Tracking

**Priority**: High  
**Description**: Track dan display student progress

**REQ-PROGRESS-001**: Calculate Progress  
**Description**: System calculate course completion percentage  
**Inputs**: User ID, course ID  
**Processing**: Count completed sessions, assignments, exams  
**Outputs**: Progress percentage (0-100)  
**Update Frequency**: Real-time setelah completion events

**REQ-PROGRESS-002**: Display Dashboard  
**Description**: Show progress overview to student  
**Inputs**: User ID  
**Processing**: Fetch enrollments, progress, grades  
**Outputs**: Dashboard dengan progress metrics, charts

---

### 3.8 Learning Path System

**Priority**: Medium  
**Description**: Structured learning journeys

**REQ-LP-001**: Create Learning Path  
**Description**: Admin create learning path  
**Inputs**: Title, description, courses sequence  
**Processing**: Validate courses, store path  
**Outputs**: Learning path created

**REQ-LP-002**: Token Generation  
**Description**: Admin generate access tokens  
**Inputs**: Learning path ID, max usage, validity period  
**Processing**: Generate unique code, store token  
**Outputs**: Token code

**REQ-LP-003**: Activate Token  
**Description**: Student activate learning path dengan token  
**Inputs**: Token code, user ID  
**Processing**: Validate token, enroll in all path courses  
**Outputs**: Learning path activated, courses enrolled

---

### 3.9 Certification System

**Priority**: Medium  
**Description**: Digital certificate issuance dan verification

**REQ-CERT-001**: Generate Certificate  
**Description**: System generate certificate upon completion  
**Inputs**: User ID, course ID  
**Processing**: Check completion criteria, generate PDF, assign number  
**Outputs**: Certificate PDF, certificate number  
**Trigger**: Automatic saat course completion

**REQ-CERT-002**: Verify Certificate  
**Description**: Public dapat verify certificate authenticity  
**Inputs**: Certificate number atau verification code  
**Processing**: Lookup certificate in database  
**Outputs**: Certificate details jika valid, error jika tidak  
**Access**: Public (no login required)

---

### 3.10 Admin Functions

**Priority**: High  
**Description**: Administrative tools

**REQ-ADMIN-001**: User Management  
**Description**: Admin manage users  
**Inputs**: User data, actions (create, edit, delete, change role)  
**Processing**: Validate permissions, execute action  
**Outputs**: User updated confirmation  
**Authorization**: Admin, SuperAdmin

**REQ-ADMIN-002**: Content Management  
**Description**: Admin manage courses, categories, learning paths  
**Inputs**: Content data, CRUD operations  
**Processing**: Validate data, execute operations  
**Outputs**: Content updated

**REQ-ADMIN-003**: Analytics Dashboard  
**Description**: View platform analytics  
**Inputs**: Date range, filters  
**Processing**: Aggregate data, calculate metrics  
**Outputs**: Charts, statistics, reports

---

## 4. External Interface Requirements

### 4.1 User Interfaces

#### 4.1.1 General UI Requirements

**REQ-UI-001**: Responsive Design

- Mobile-first approach
- Breakpoints: 375px, 768px, 1024px, 1280px
- Touch-friendly elements (min 44x44px)

**REQ-UI-002**: Consistent Design System

- Follow Tailwind CSS conventions
- Consistent color scheme (primary blue)
- Typography: Poppins font family
- Standard components: buttons, forms, cards

**REQ-UI-003**: Accessibility

- WCAG 2.1 Level AA compliance
- Keyboard navigation support
- Screen reader compatible
- Sufficient color contrast (4.5:1)

#### 4.1.2 Key User Interfaces

**Landing Page**:

- Hero section dengan value proposition
- Featured courses
- Statistics showcase
- Call-to-action buttons

**Course Catalog**:

- Grid layout dengan course cards
- Search bar
- Category filters
- Pagination

**Course Detail**:

- Course overview
- Syllabus/session list
- Instructor information
- Enroll button
- Reviews/ratings

**Dashboard** (per role):

- Overview metrics (cards)
- Recent activity
- Quick actions
- Navigation sidebar

**Learning Interface**:

- Session content area (main)
- Session navigation (sidebar)
- Progress indicator
- Assignment/exam sections

### 4.2 Hardware Interfaces

**REQ-HW-001**: Camera Access

- For QR code scanning
- Request permission from browser
- Fallback to manual code entry

### 4.3 Software Interfaces

#### 4.3.1 Backend API

**REQ-API-001**: REST API Communication

- Protocol: HTTPS
- Format: JSON
- Authentication: JWT Bearer token
- Base URL: Configurable via environment variable

**Standard Response Format**:

```json
{
  "data": {
    /* response data */
  },
  "meta": {
    "status": 200,
    "message": "Success",
    "timestamp": "2025-11-04T10:00:00Z"
  }
}
```

**Standard Error Format**:

```json
{
  "meta": {
    "status": 400,
    "message": "Error description",
    "errors": {
      /* field errors */
    }
  }
}
```

#### 4.3.2 Google OAuth

**REQ-EXT-001**: Google OAuth Integration

- OAuth 2.0 protocol
- Scopes: email, profile
- Redirect URL: Configurable
- Error handling: Network failures, user cancellation

#### 4.3.3 reCAPTCHA

**REQ-EXT-002**: reCAPTCHA Integration

- Version: v2 or v3
- Placement: Registration, login forms
- Validation: Server-side verification

### 4.4 Communication Interfaces

**REQ-COMM-001**: HTTP/HTTPS

- All API communication over HTTPS
- TLS 1.2 or higher
- Certificate validation

**REQ-COMM-002**: WebSocket (Future)

- For real-time features
- Notifications, live updates

---

## 5. Non-Functional Requirements

### 5.1 Performance Requirements

**REQ-PERF-001**: Response Time

- Page load: < 2 seconds (desktop), < 3 seconds (mobile)
- API response: < 500ms average
- Database query: < 200ms

**REQ-PERF-002**: Throughput

- Support 500+ concurrent users
- Handle 1000+ requests per minute

**REQ-PERF-003**: Resource Usage

- Client memory: < 150MB
- Bundle size: < 1MB (initial load)

### 5.2 Safety Requirements

**REQ-SAFE-001**: Data Backup

- Daily full backups
- Hourly incremental backups
- 30-day retention

**REQ-SAFE-002**: Disaster Recovery

- Recovery Time Objective (RTO): 4 hours
- Recovery Point Objective (RPO): 1 hour

### 5.3 Security Requirements

**REQ-SEC-001**: Authentication

- JWT-based dengan expiration
- Secure token storage (HTTP-only cookies)
- Password hashing (bcrypt/Argon2)

**REQ-SEC-002**: Authorization

- Role-based access control
- Route protection (middleware)
- API endpoint authorization

**REQ-SEC-003**: Data Protection

- SQL injection prevention
- XSS protection
- CSRF protection
- Input validation dan sanitization

**REQ-SEC-004**: Secure Communication

- HTTPS only in production
- TLS 1.2+
- Certificate validation

**REQ-SEC-005**: Privacy

- User consent for data collection
- Data minimization
- Right to deletion (GDPR-like)

### 5.4 Software Quality Attributes

#### 5.4.1 Reliability

**REQ-QUAL-001**: Availability

- Target: 99.5% uptime
- Planned maintenance windows
- Graceful degradation

**REQ-QUAL-002**: Fault Tolerance

- Error boundaries for React components
- API retry logic
- Fallback UI for errors

#### 5.4.2 Maintainability

**REQ-QUAL-003**: Code Quality

- TypeScript untuk type safety
- ESLint compliance
- Code documentation
- Modular architecture

**REQ-QUAL-004**: Testing

- Unit tests (coverage > 70%)
- Integration tests untuk critical flows
- E2E tests untuk user journeys

#### 5.4.3 Usability

**REQ-QUAL-005**: Ease of Use

- Intuitive navigation
- Clear error messages
- Helpful tooltips
- Onboarding tutorials

**REQ-QUAL-006**: Learnability

- Consistent UI patterns
- Contextual help
- Documentation available

#### 5.4.4 Scalability

**REQ-QUAL-007**: Horizontal Scaling

- Stateless application design
- Load balancer ready
- Database connection pooling

### 5.5 Business Rules

**REQ-BUS-001**: Enrollment Rules

- Student dapat enroll multiple courses
- One enrollment per class per student
- Cannot enroll jika class full

**REQ-BUS-002**: Assignment Rules

- Late submissions allowed dengan penalty (configurable)
- Cannot edit submission setelah graded
- Score must be between 0 dan max_score

**REQ-BUS-003**: Attendance Rules

- QR code valid selama session time + buffer
- One check-in per session per student
- Late check-in configurable (e.g., 15 minutes)

**REQ-BUS-004**: Certificate Rules

- Issued upon course completion (100% progress + passing grade)
- Unique certificate number
- Cannot be revoked once issued

**REQ-BUS-005**: Token Rules

- One-time use default (configurable max usage)
- Expiration date enforced
- Cannot redeem twice oleh same user

---

## 6. Appendices

### Appendix A: User Story Summary

Total user stories: 50+  
Priority breakdown:

- High: 30 stories
- Medium: 15 stories
- Low: 5 stories

See [User Story Map](project-planning-User-Story-Map) untuk detail.

### Appendix B: Technical Stack Summary

| Component          | Technology   | Version |
| ------------------ | ------------ | ------- |
| Frontend Framework | Next.js      | 14.2.3  |
| UI Library         | React        | 18.3.1  |
| Language           | TypeScript   | Latest  |
| Styling            | Tailwind CSS | Latest  |
| State Management   | Jotai        | 2.12.5  |
| HTTP Client        | Axios        | 1.10.0  |
| Package Manager    | Bun          | Latest  |

### Appendix C: API Endpoint Summary

Total endpoints: 100+  
Categories:

- Authentication: 5 endpoints
- Courses: 15 endpoints
- Classes: 12 endpoints
- Assignments: 10 endpoints
- Attendance: 8 endpoints
- Learning Paths: 10 endpoints
- Certificates: 5 endpoints
- Admin: 20+ endpoints

### Appendix D: Database Summary

Total tables: 20+  
Primary entities:

- User, Role
- Course, Category, Class, Session
- Assignment, Submission, Exam, Exam_Result
- Enrollment, Attendance, Activity_Log
- Learning_Path, Learning_Path_Item, Token
- Certificate

See [Database Schema](database-schema) untuk detail.

### Appendix E: Glossary

Refer to Section 1.3 untuk definitions.

### Appendix F: Change Log

| Date     | Version | Changes              | Author   |
| -------- | ------- | -------------------- | -------- |
| Nov 2025 | 1.0     | Initial SRS document | Dev Team |

---

## Approval

| Role            | Name | Signature | Date |
| --------------- | ---- | --------- | ---- |
| Project Manager |      |           |      |
| Lead Developer  |      |           |      |
| QA Lead         |      |           |      |
| Stakeholder     |      |           |      |

---

**END OF DOCUMENT**

---

[← Back: Database Schema](database-schema.md) | [Back to Home →](Home.md)
