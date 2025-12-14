# Database Schema (Full Documentation)

## Table of Contents

1. [Overview](#1-overview)
2. [Entity Relationship Diagram](#2-entity-relationship-diagram)
3. [Table Specifications](#3-table-specifications)
4. [Relationships](#4-relationships)
5. [Indexes & Constraints](#5-indexes--constraints)
6. [Query Examples](#6-query-examples)
7. [Data Dictionary](#7-data-dictionary)

---

## 1. Overview

Database Bengkel Koding Web V2 dirancang untuk mendukung comprehensive learning management system dengan fokus pada:

- **User Management** - Multi-role user system
- **Course Management** - Courses, classes, sessions
- **Assessment** - Assignments, exams, submissions
- **Tracking** - Progress, attendance, activity logs
- **Learning Paths** - Structured learning journeys
- **Certification** - Digital certificates

### Database Technology

- **DBMS**: PostgreSQL (Recommended) / MySQL
- **Normalization**: 3NF (Third Normal Form)
- **Indexing**: Strategic indexes untuk performance
- **Constraints**: Foreign keys, unique constraints, check constraints

---

## 2. Entity Relationship Diagram

[_ERD diagram sudah ada di file development-guide-Database-Schema.md_]

```mermaid
erDiagram
    USER ||--o{ ENROLLMENT : enrolls
    USER ||--o{ SUBMISSION : submits
    USER ||--o{ ATTENDANCE : attends
    USER ||--o{ ACTIVITY_LOG : logs
    USER }o--|| ROLE : has

    COURSE ||--o{ CLASS : contains
    COURSE ||--o{ ENROLLMENT : has
    COURSE }o--|| CATEGORY : belongs_to

    CLASS ||--o{ SESSION : has
    CLASS ||--o{ ATTENDANCE : tracks
    CLASS }o--|| USER : taught_by
    CLASS }o--|| USER : assisted_by

    SESSION ||--o{ ASSIGNMENT : contains
    SESSION ||--o{ EXAM : contains

    ASSIGNMENT ||--o{ SUBMISSION : has
    EXAM ||--o{ EXAM_RESULT : has

    LEARNING_PATH ||--o{ LEARNING_PATH_ITEM : contains
    LEARNING_PATH ||--o{ TOKEN : has
    LEARNING_PATH_ITEM }o--|| COURSE : references

    USER ||--o{ CERTIFICATE : earns
    COURSE ||--o{ CERTIFICATE : issues
```

---

## 3. Table Specifications

### 3.1 User Management Tables

#### USER Table

```sql
CREATE TABLE user (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255) NOT NULL,
    nim_nidn VARCHAR(50),
    role VARCHAR(20) NOT NULL CHECK (role IN ('student', 'asisten', 'dosen', 'admin', 'superadmin')),
    avatar TEXT,
    email_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_user_email ON user(email);
CREATE INDEX idx_user_role ON user(role);
CREATE INDEX idx_user_nim_nidn ON user(nim_nidn);
```

**Description**: Central user table storing all user types  
**Records**: ~10,000+ users  
**Growth Rate**: ~2,000 new users/semester

#### ROLE Table

```sql
CREATE TABLE role (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    permissions JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO role (name, description) VALUES
('student', 'Regular student accessing courses'),
('asisten', 'Teaching assistant helping lecturers'),
('dosen', 'Lecturer creating and managing courses'),
('admin', 'Administrator managing platform content'),
('superadmin', 'Super administrator with full access');
```

---

### 3.2 Course Management Tables

#### CATEGORY Table

```sql
CREATE TABLE category (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    icon VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_category_slug ON category(slug);
```

**Sample Data**:

- Web Development
- Mobile Development
- Data Science
- DevOps
- Database Management

#### COURSE Table

```sql
CREATE TABLE course (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    thumbnail TEXT,
    category_id INTEGER REFERENCES category(id),
    instructor_id INTEGER REFERENCES user(id),
    level VARCHAR(20) CHECK (level IN ('beginner', 'intermediate', 'advanced')),
    duration INTEGER, -- in hours
    prerequisites TEXT,
    learning_objectives TEXT,
    is_published BOOLEAN DEFAULT FALSE,
    views INTEGER DEFAULT 0,
    enrollments_count INTEGER DEFAULT 0,
    rating DECIMAL(3,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_course_slug ON course(slug);
CREATE INDEX idx_course_category ON course(category_id);
CREATE INDEX idx_course_instructor ON course(instructor_id);
CREATE INDEX idx_course_published ON course(is_published);
```

**Description**: Core course information  
**Records**: ~100+ courses  
**Growth Rate**: ~20 new courses/semester

#### CLASS Table

```sql
CREATE TABLE class (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    course_id INTEGER REFERENCES course(id) ON DELETE RESTRICT,
    instructor_id INTEGER REFERENCES user(id),
    assistant_id INTEGER REFERENCES user(id),
    start_date DATE,
    end_date DATE,
    status VARCHAR(20) CHECK (status IN ('active', 'completed', 'cancelled')),
    max_students INTEGER DEFAULT 30,
    enrolled_count INTEGER DEFAULT 0,
    meeting_schedule VARCHAR(255),
    meeting_room VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_class_course ON class(course_id);
CREATE INDEX idx_class_instructor ON class(instructor_id);
CREATE INDEX idx_class_status ON class(status);
```

---

### 3.3 Learning Content Tables

#### SESSION Table

```sql
CREATE TABLE session (
    id SERIAL PRIMARY KEY,
    class_id INTEGER REFERENCES class(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    session_number INTEGER NOT NULL,
    description TEXT,
    content LONGTEXT, -- Markdown content
    session_date DATE,
    start_time TIME,
    end_time TIME,
    meeting_link VARCHAR(500),
    materials JSON, -- Array of material URLs
    is_published BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    UNIQUE(class_id, session_number)
);

CREATE INDEX idx_session_class ON session(class_id);
CREATE INDEX idx_session_date ON session(session_date);
```

---

### 3.4 Assessment Tables

#### ASSIGNMENT Table

```sql
CREATE TABLE assignment (
    id SERIAL PRIMARY KEY,
    session_id INTEGER REFERENCES session(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    instruction LONGTEXT,
    deadline TIMESTAMP NOT NULL,
    max_score INTEGER NOT NULL,
    type VARCHAR(20) CHECK (type IN ('homework', 'project', 'quiz', 'lab')),
    attachments JSON,
    is_published BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_assignment_session ON assignment(session_id);
CREATE INDEX idx_assignment_deadline ON assignment(deadline);
```

#### SUBMISSION Table

```sql
CREATE TABLE submission (
    id SERIAL PRIMARY KEY,
    assignment_id INTEGER REFERENCES assignment(id) ON DELETE CASCADE,
    student_id INTEGER REFERENCES user(id),
    content TEXT,
    file_url TEXT,
    files JSON, -- Multiple file URLs
    score DECIMAL(5,2),
    feedback TEXT,
    status VARCHAR(20) CHECK (status IN ('draft', 'submitted', 'graded', 'late')),
    submitted_at TIMESTAMP,
    graded_at TIMESTAMP,
    graded_by INTEGER REFERENCES user(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    UNIQUE(assignment_id, student_id)
);

CREATE INDEX idx_submission_assignment ON submission(assignment_id);
CREATE INDEX idx_submission_student ON submission(student_id);
CREATE INDEX idx_submission_status ON submission(status);
```

#### EXAM Table

```sql
CREATE TABLE exam (
    id SERIAL PRIMARY KEY,
    session_id INTEGER REFERENCES session(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    instructions TEXT,
    start_time TIMESTAMP NOT NULL,
    end_time TIMESTAMP NOT NULL,
    duration INTEGER NOT NULL, -- in minutes
    max_score INTEGER NOT NULL,
    passing_score INTEGER,
    questions JSON, -- Question bank
    is_published BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_exam_session ON exam(session_id);
CREATE INDEX idx_exam_start_time ON exam(start_time);
```

#### EXAM_RESULT Table

```sql
CREATE TABLE exam_result (
    id SERIAL PRIMARY KEY,
    exam_id INTEGER REFERENCES exam(id) ON DELETE CASCADE,
    student_id INTEGER REFERENCES user(id),
    answers JSON, -- Student's answers
    score DECIMAL(5,2),
    percentage DECIMAL(5,2),
    status VARCHAR(20) CHECK (status IN ('in_progress', 'submitted', 'graded')),
    started_at TIMESTAMP,
    submitted_at TIMESTAMP,
    time_spent INTEGER, -- in seconds
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    UNIQUE(exam_id, student_id)
);

CREATE INDEX idx_exam_result_exam ON exam_result(exam_id);
CREATE INDEX idx_exam_result_student ON exam_result(student_id);
```

---

### 3.5 Tracking Tables

#### ENROLLMENT Table

```sql
CREATE TABLE enrollment (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES user(id),
    course_id INTEGER REFERENCES course(id),
    class_id INTEGER REFERENCES class(id),
    status VARCHAR(20) CHECK (status IN ('active', 'completed', 'dropped', 'failed')),
    progress DECIMAL(5,2) DEFAULT 0.00 CHECK (progress >= 0 AND progress <= 100),
    final_score DECIMAL(5,2),
    enrolled_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP,
    last_accessed_at TIMESTAMP,
    UNIQUE(user_id, class_id)
);

CREATE INDEX idx_enrollment_user ON enrollment(user_id);
CREATE INDEX idx_enrollment_course ON enrollment(course_id);
CREATE INDEX idx_enrollment_class ON enrollment(class_id);
CREATE INDEX idx_enrollment_status ON enrollment(status);
```

**Business Rules**:

- One student can only enroll once per class
- Progress updates automatically based on session completion
- Status changes to 'completed' when progress = 100%

#### ATTENDANCE Table

```sql
CREATE TABLE attendance (
    id SERIAL PRIMARY KEY,
    class_id INTEGER REFERENCES class(id),
    session_id INTEGER REFERENCES session(id),
    student_id INTEGER REFERENCES user(id),
    status VARCHAR(20) CHECK (status IN ('present', 'absent', 'late', 'excused')),
    check_in_time TIMESTAMP,
    qr_code VARCHAR(255),
    latitude DECIMAL(10,8),
    longitude DECIMAL(11,8),
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(session_id, student_id)
);

CREATE INDEX idx_attendance_class ON attendance(class_id);
CREATE INDEX idx_attendance_session ON attendance(session_id);
CREATE INDEX idx_attendance_student ON attendance(student_id);
CREATE INDEX idx_attendance_status ON attendance(status);
```

#### ACTIVITY_LOG Table

```sql
CREATE TABLE activity_log (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES user(id),
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(50),
    entity_id INTEGER,
    description TEXT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    metadata JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_activity_log_user ON activity_log(user_id);
CREATE INDEX idx_activity_log_action ON activity_log(action);
CREATE INDEX idx_activity_log_entity ON activity_log(entity_type, entity_id);
CREATE INDEX idx_activity_log_created ON activity_log(created_at);
```

**Purpose**: Audit trail dan user behavior tracking

---

### 3.6 Learning Path Tables

#### LEARNING_PATH Table

```sql
CREATE TABLE learning_path (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    thumbnail TEXT,
    level INTEGER CHECK (level BETWEEN 1 AND 5),
    estimated_duration INTEGER, -- in hours
    prerequisites TEXT,
    learning_outcomes TEXT,
    is_published BOOLEAN DEFAULT FALSE,
    enrollments_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_learning_path_slug ON learning_path(slug);
CREATE INDEX idx_learning_path_published ON learning_path(is_published);
```

#### LEARNING_PATH_ITEM Table

```sql
CREATE TABLE learning_path_item (
    id SERIAL PRIMARY KEY,
    learning_path_id INTEGER REFERENCES learning_path(id) ON DELETE CASCADE,
    course_id INTEGER REFERENCES course(id),
    sequence INTEGER NOT NULL,
    is_required BOOLEAN DEFAULT TRUE,
    unlock_condition TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(learning_path_id, course_id),
    UNIQUE(learning_path_id, sequence)
);

CREATE INDEX idx_lp_item_path ON learning_path_item(learning_path_id);
CREATE INDEX idx_lp_item_course ON learning_path_item(course_id);
```

#### TOKEN Table

```sql
CREATE TABLE token (
    id SERIAL PRIMARY KEY,
    code VARCHAR(50) UNIQUE NOT NULL,
    learning_path_id INTEGER REFERENCES learning_path(id),
    max_usage INTEGER DEFAULT 1,
    used_count INTEGER DEFAULT 0,
    valid_from TIMESTAMP,
    valid_until TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    created_by INTEGER REFERENCES user(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CHECK (used_count <= max_usage)
);

CREATE INDEX idx_token_code ON token(code);
CREATE INDEX idx_token_learning_path ON token(learning_path_id);
CREATE INDEX idx_token_active ON token(is_active);
```

#### TOKEN_REDEMPTION Table

```sql
CREATE TABLE token_redemption (
    id SERIAL PRIMARY KEY,
    token_id INTEGER REFERENCES token(id),
    user_id INTEGER REFERENCES user(id),
    redeemed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ip_address VARCHAR(45),
    UNIQUE(token_id, user_id)
);

CREATE INDEX idx_token_redemption_token ON token_redemption(token_id);
CREATE INDEX idx_token_redemption_user ON token_redemption(user_id);
```

---

### 3.7 Certification Tables

#### CERTIFICATE Table

```sql
CREATE TABLE certificate (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES user(id),
    course_id INTEGER REFERENCES course(id),
    certificate_number VARCHAR(100) UNIQUE NOT NULL,
    certificate_url TEXT,
    issue_date DATE NOT NULL,
    expiry_date DATE,
    verification_code VARCHAR(50) UNIQUE,
    metadata JSON,
    issued_by INTEGER REFERENCES user(id),
    is_valid BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_certificate_number ON certificate(certificate_number);
CREATE INDEX idx_certificate_user ON certificate(user_id);
CREATE INDEX idx_certificate_course ON certificate(course_id);
CREATE INDEX idx_certificate_verification ON certificate(verification_code);
```

---

## 4. Relationships

### 4.1 One-to-Many Relationships

| Parent Table | Child Table | Relationship                         |
| ------------ | ----------- | ------------------------------------ |
| USER         | ENROLLMENT  | One user has many enrollments        |
| USER         | SUBMISSION  | One user has many submissions        |
| USER         | ATTENDANCE  | One user has many attendance records |
| COURSE       | CLASS       | One course has many classes          |
| CLASS        | SESSION     | One class has many sessions          |
| SESSION      | ASSIGNMENT  | One session has many assignments     |
| ASSIGNMENT   | SUBMISSION  | One assignment has many submissions  |

### 4.2 Many-to-Many Relationships

| Entity 1      | Entity 2 | Junction Table     |
| ------------- | -------- | ------------------ |
| LEARNING_PATH | COURSE   | LEARNING_PATH_ITEM |
| USER          | COURSE   | ENROLLMENT         |

---

## 5. Indexes & Constraints

### 5.1 Primary Keys

- Auto-incrementing INTEGER untuk all tables
- Named: `id`

### 5.2 Foreign Keys

- All foreign keys have proper ON DELETE behavior
- CASCADE untuk dependent records
- RESTRICT untuk critical references

### 5.3 Unique Constraints

```sql
-- Unique emails
ALTER TABLE user ADD CONSTRAINT uk_user_email UNIQUE (email);

-- Unique certificate numbers
ALTER TABLE certificate ADD CONSTRAINT uk_certificate_number UNIQUE (certificate_number);

-- Unique course slugs
ALTER TABLE course ADD CONSTRAINT uk_course_slug UNIQUE (slug);

-- One submission per student per assignment
ALTER TABLE submission ADD CONSTRAINT uk_submission_student_assignment
    UNIQUE (assignment_id, student_id);
```

### 5.4 Check Constraints

```sql
-- Progress percentage
ALTER TABLE enrollment ADD CONSTRAINT chk_enrollment_progress
    CHECK (progress >= 0 AND progress <= 100);

-- Score ranges
ALTER TABLE submission ADD CONSTRAINT chk_submission_score
    CHECK (score >= 0 AND score <= max_score);

-- Token usage
ALTER TABLE token ADD CONSTRAINT chk_token_usage
    CHECK (used_count <= max_usage);

-- Valid roles
ALTER TABLE user ADD CONSTRAINT chk_user_role
    CHECK (role IN ('student', 'asisten', 'dosen', 'admin', 'superadmin'));
```

---

## 6. Query Examples

### 6.1 Common Queries

#### Get Student's Enrolled Courses

```sql
SELECT
    c.id,
    c.title,
    c.thumbnail,
    cat.name as category,
    e.progress,
    e.status,
    e.enrolled_at
FROM enrollment e
JOIN course c ON e.course_id = c.id
JOIN category cat ON c.category_id = cat.id
WHERE e.user_id = ?
    AND e.status = 'active'
ORDER BY e.enrolled_at DESC;
```

#### Get Class Attendance Statistics

```sql
SELECT
    cl.name as class_name,
    COUNT(DISTINCT s.id) as total_sessions,
    COUNT(CASE WHEN a.status = 'present' THEN 1 END) as present_count,
    COUNT(CASE WHEN a.status = 'absent' THEN 1 END) as absent_count,
    ROUND(
        COUNT(CASE WHEN a.status = 'present' THEN 1 END) * 100.0 /
        (COUNT(DISTINCT s.id) * (SELECT COUNT(*) FROM enrollment WHERE class_id = cl.id)),
        2
    ) as attendance_rate
FROM class cl
LEFT JOIN session s ON s.class_id = cl.id
LEFT JOIN attendance a ON a.session_id = s.id
WHERE cl.id = ?
GROUP BY cl.id;
```

#### Get Student's Assignment Submissions with Grades

```sql
SELECT
    a.title,
    a.deadline,
    a.max_score,
    s.score,
    s.feedback,
    s.submitted_at,
    s.status,
    CASE
        WHEN s.submitted_at > a.deadline THEN 'Late'
        ELSE 'On Time'
    END as submission_status
FROM assignment a
LEFT JOIN submission s ON s.assignment_id = a.id AND s.student_id = ?
JOIN session sess ON a.session_id = sess.id
JOIN class c ON sess.class_id = c.id
WHERE c.id = ?
ORDER BY a.deadline ASC;
```

#### Get Learning Path Progress

```sql
SELECT
    lp.title as learning_path_title,
    lpi.sequence,
    c.title as course_title,
    e.progress as course_progress,
    e.status as enrollment_status,
    lpi.is_required
FROM learning_path lp
JOIN learning_path_item lpi ON lpi.learning_path_id = lp.id
JOIN course c ON c.id = lpi.course_id
LEFT JOIN enrollment e ON e.course_id = c.id AND e.user_id = ?
WHERE lp.id = ?
ORDER BY lpi.sequence;
```

### 6.2 Analytics Queries

#### Top Courses by Enrollment

```sql
SELECT
    c.title,
    c.thumbnail,
    COUNT(e.id) as enrollment_count,
    AVG(e.progress) as avg_progress,
    COUNT(CASE WHEN e.status = 'completed' THEN 1 END) as completed_count
FROM course c
LEFT JOIN enrollment e ON e.course_id = c.id
WHERE c.is_published = TRUE
GROUP BY c.id
ORDER BY enrollment_count DESC
LIMIT 10;
```

#### Student Performance Report

```sql
SELECT
    u.name,
    u.nim_nidn,
    COUNT(DISTINCT e.course_id) as courses_enrolled,
    AVG(e.progress) as avg_progress,
    COUNT(DISTINCT CASE WHEN e.status = 'completed' THEN e.course_id END) as courses_completed,
    AVG(sub.score) as avg_assignment_score,
    COUNT(DISTINCT a.id) as attendance_count,
    ROUND(
        COUNT(CASE WHEN a.status = 'present' THEN 1 END) * 100.0 /
        COUNT(DISTINCT a.id),
        2
    ) as attendance_rate
FROM user u
LEFT JOIN enrollment e ON e.user_id = u.id
LEFT JOIN submission sub ON sub.student_id = u.id
LEFT JOIN attendance a ON a.student_id = u.id
WHERE u.id = ?
GROUP BY u.id;
```

---

## 7. Data Dictionary

### 7.1 Common Field Conventions

| Field Pattern | Type      | Description           |
| ------------- | --------- | --------------------- |
| `id`          | SERIAL    | Primary key           |
| `*_id`        | INTEGER   | Foreign key reference |
| `*_at`        | TIMESTAMP | Timestamp fields      |
| `is_*`        | BOOLEAN   | Boolean flags         |
| `*_count`     | INTEGER   | Counter fields        |
| `*_url`       | TEXT      | URL fields            |

### 7.2 Status Enumerations

#### Enrollment Status

- `active` - Currently enrolled
- `completed` - Successfully completed
- `dropped` - Dropped by student
- `failed` - Did not meet requirements

#### Assignment/Submission Status

- `draft` - Work in progress
- `submitted` - Submitted for grading
- `graded` - Graded by instructor
- `late` - Submitted after deadline

#### Attendance Status

- `present` - Attended on time
- `absent` - Did not attend
- `late` - Attended late
- `excused` - Excused absence

#### Class Status

- `active` - Currently running
- `completed` - Finished
- `cancelled` - Cancelled

---

## 8. Database Maintenance

### 8.1 Backup Strategy

- Daily full backups
- Hourly incremental backups
- Retention: 30 days

### 8.2 Performance Optimization

- Regular ANALYZE untuk statistics update
- Periodic VACUUM untuk cleanup
- Index maintenance
- Query optimization

### 8.3 Data Archival

- Archive completed courses setelah 2 tahun
- Archive old activity logs (> 1 year)
- Maintain referential integrity

---

[← Back: Technical Architectures](technical-architectures.md) | [Next: SRS Document →](srs.md)
