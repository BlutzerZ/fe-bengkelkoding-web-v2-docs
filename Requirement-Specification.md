# Requirement Specification - Bengkel Koding Web V2

## Document Overview

Dokumen ini menjelaskan spesifikasi kebutuhan fungsional dan non-fungsional untuk platform Bengkel Koding Web V2.

---

## 1. Functional Requirements

### 1.1 User Management

#### FR-UM-01: User Registration

**Description**: System harus mendukung registrasi user baru  
**Priority**: High  
**Details**:

- Email registration dengan verifikasi
- Google OAuth integration
- Password requirements: min 8 karakter, kombinasi huruf dan angka
- reCAPTCHA untuk keamanan
- Email verification sebelum aktivasi akun

#### FR-UM-02: User Authentication

**Description**: System harus autentikasi user dengan secure login  
**Priority**: High  
**Details**:

- Login dengan email/password
- Login dengan Google OAuth
- JWT-based authentication
- Session management dengan cookies
- Remember me functionality
- Password reset via email

#### FR-UM-03: User Authorization

**Description**: System harus enforce role-based access control  
**Priority**: High  
**Details**:

- 5 user roles: student, asisten, dosen, admin, superadmin
- Role-specific dashboard dan permissions
- Route protection berdasarkan role
- Unauthorized access redirect

#### FR-UM-04: Profile Management

**Description**: User dapat mengelola profil mereka  
**Priority**: Medium  
**Details**:

- Update personal information
- Change password
- Upload avatar/profile picture
- View activity history

---

### 1.2 Course Management

#### FR-CM-01: Course Creation

**Description**: Dosen/Admin dapat membuat course baru  
**Priority**: High  
**Details**:

- Course metadata (title, description, level, duration)
- Course thumbnail upload
- Category assignment
- Publish/draft status
- Syllabus management

#### FR-CM-02: Course Catalog

**Description**: User dapat browse dan search courses  
**Priority**: High  
**Details**:

- List all published courses
- Filter by category
- Search by keyword
- Sort by popularity, date, rating
- Course detail view dengan full information

#### FR-CM-03: Course Enrollment

**Description**: Student dapat enroll dalam courses  
**Priority**: High  
**Details**:

- One-click enrollment untuk free courses
- Token-based enrollment untuk restricted courses
- Enrollment confirmation
- Max student capacity check
- Enrollment status tracking

---

### 1.3 Class Management

#### FR-CL-01: Class Creation

**Description**: Dosen dapat membuat class instances  
**Priority**: High  
**Details**:

- Associate class dengan course
- Set start/end dates
- Assign instructor dan assistant
- Set max student capacity
- Class schedule management

#### FR-CL-02: Session Management

**Description**: Dosen dapat mengelola class sessions  
**Priority**: High  
**Details**:

- Create sessions dengan sequential numbering
- Set session date dan time
- Add session content (Markdown support)
- Attach meeting links
- Upload materials (documents, videos)

---

### 1.4 Learning Content

#### FR-LC-01: Content Delivery

**Description**: Student dapat mengakses learning materials  
**Priority**: High  
**Details**:

- Rich text content rendering (Markdown)
- Code syntax highlighting
- Embedded video support
- Downloadable materials
- LaTeX math formula support

#### FR-LC-02: Progress Tracking

**Description**: System track student learning progress  
**Priority**: High  
**Details**:

- Session completion tracking
- Overall course progress percentage
- Last accessed content
- Time spent tracking

---

### 1.5 Assignment Management

#### FR-AM-01: Assignment Creation

**Description**: Dosen dapat membuat assignments  
**Priority**: High  
**Details**:

- Assignment title, description, instructions
- Set deadline
- Define max score
- Assignment type (homework, project, quiz)
- Publish/draft status
- File attachment support

#### FR-AM-02: Assignment Submission

**Description**: Student dapat submit assignments  
**Priority**: High  
**Details**:

- Text submission
- File upload (multiple files)
- Submission before deadline validation
- Late submission handling
- Edit submission before grading

#### FR-AM-03: Assignment Grading

**Description**: Dosen/Asisten dapat grade submissions  
**Priority**: High  
**Details**:

- Assign numerical score
- Written feedback
- Rubric-based grading (optional)
- Bulk grading tools
- Grade notification to students

---

### 1.6 Examination

#### FR-EX-01: Exam Creation

**Description**: Dosen dapat membuat online exams  
**Priority**: Medium  
**Details**:

- Exam title dan description
- Start dan end time
- Time limit (duration)
- Max score
- Question bank

#### FR-EX-02: Exam Taking

**Description**: Student dapat mengikuti exams  
**Priority**: Medium  
**Details**:

- Time-bound exam interface
- Auto-save answers
- Submit before time expires
- Timer display
- Warning notifications

#### FR-EX-03: Exam Results

**Description**: System calculate dan display exam results  
**Priority**: Medium  
**Details**:

- Automatic grading (for objective questions)
- Score calculation
- Result display after submission
- Answer review (if allowed)

---

### 1.7 Attendance Management

#### FR-AT-01: QR Code Generation

**Description**: Dosen/Asisten dapat generate QR codes  
**Priority**: Medium  
**Details**:

- Generate unique QR code per session
- Time-limited validity
- QR code display interface

#### FR-AT-02: Attendance Marking

**Description**: Student dapat mark attendance via QR  
**Priority**: Medium  
**Details**:

- QR code scanning
- Location validation (optional)
- Timestamp recording
- Attendance status (present, late, absent, excused)

#### FR-AT-03: Attendance Tracking

**Description**: System track attendance records  
**Priority**: Medium  
**Details**:

- Attendance history per student
- Class attendance rate
- Attendance reports
- Manual attendance adjustment

---

### 1.8 Learning Path

#### FR-LP-01: Learning Path Creation

**Description**: Admin dapat membuat learning paths  
**Priority**: Medium  
**Details**:

- Learning path metadata
- Add multiple courses in sequence
- Set required vs optional courses
- Estimated completion time
- Learning path thumbnail

#### FR-LP-02: Token Management

**Description**: Admin dapat generate access tokens  
**Priority**: Medium  
**Details**:

- Generate unique token codes
- Set max usage limit
- Set validity period
- Track token redemption
- Deactivate tokens

#### FR-LP-03: Learning Path Enrollment

**Description**: Student dapat activate learning path dengan token  
**Priority**: Medium  
**Details**:

- Token redemption interface
- Token validation
- Auto-enrollment in path courses
- Progress tracking across path

---

### 1.9 Certification

#### FR-CR-01: Certificate Generation

**Description**: System generate certificates untuk course completion  
**Priority**: Medium  
**Details**:

- Automatic certificate upon completion criteria met
- Unique certificate number
- Certificate PDF generation
- Digital signature/seal
- Certificate metadata (issue date, course info)

#### FR-CR-02: Certificate Verification

**Description**: Public dapat verify certificate authenticity  
**Priority**: Medium  
**Details**:

- Public verification page
- Certificate number lookup
- Display certificate details
- Show verification status

---

### 1.10 Admin Functions

#### FR-AD-01: User Management

**Description**: Admin dapat mengelola all users  
**Priority**: High  
**Details**:

- View all users dengan filtering
- Search users
- Edit user information
- Change user roles
- Deactivate/activate accounts

#### FR-AD-02: Content Management

**Description**: Admin dapat mengelola platform content  
**Priority**: High  
**Details**:

- Manage courses, categories, learning paths
- FAQ management
- Feedback review dan response
- Image/asset management

#### FR-AD-03: Analytics Dashboard

**Description**: Admin dapat view platform analytics  
**Priority**: Medium  
**Details**:

- User registration trends
- Course enrollment statistics
- Completion rates
- Popular courses
- Active users

---

## 2. Non-Functional Requirements

### 2.1 Performance

#### NFR-PF-01: Response Time

**Description**: System harus respond cepat untuk user interactions  
**Requirements**:

- Page load time: < 2 seconds (desktop)
- API response time: < 500ms (average)
- Database query time: < 200ms

#### NFR-PF-02: Concurrent Users

**Description**: System harus handle multiple concurrent users  
**Requirements**:

- Support 500+ concurrent users
- No performance degradation dengan load
- Proper load balancing

#### NFR-PF-03: Scalability

**Description**: System harus scalable untuk growth  
**Requirements**:

- Horizontal scaling capability
- Database optimization untuk large datasets
- CDN untuk static assets

---

### 2.2 Security

#### NFR-SC-01: Authentication Security

**Description**: System harus implement secure authentication  
**Requirements**:

- JWT dengan secure token storage
- Token expiration dan refresh
- HTTPS only untuk production
- Password hashing (bcrypt/Argon2)

#### NFR-SC-02: Authorization

**Description**: System harus enforce strict authorization  
**Requirements**:

- Role-based access control (RBAC)
- Middleware-level protection
- API endpoint authorization

#### NFR-SC-03: Data Protection

**Description**: System harus protect sensitive data  
**Requirements**:

- SQL injection prevention
- XSS protection
- CSRF protection
- Input validation dan sanitization
- Secure file upload

#### NFR-SC-04: Privacy

**Description**: System harus respect user privacy  
**Requirements**:

- GDPR compliance considerations
- Data encryption at rest
- Secure data transmission (TLS)
- Privacy policy implementation

---

### 2.3 Usability

#### NFR-US-01: User Interface

**Description**: System harus have intuitive UI  
**Requirements**:

- Consistent design system
- Responsive design (mobile, tablet, desktop)
- Clear navigation
- Helpful error messages

#### NFR-US-02: Accessibility

**Description**: System harus accessible untuk all users  
**Requirements**:

- WCAG 2.1 Level AA compliance
- Keyboard navigation support
- Screen reader compatibility
- Sufficient color contrast

#### NFR-US-03: Internationalization

**Description**: System harus support multiple languages (future)  
**Requirements**:

- i18n framework integration
- Multi-language support structure
- Right-to-left (RTL) layout support

---

### 2.4 Reliability

#### NFR-RL-01: Availability

**Description**: System harus highly available  
**Requirements**:

- 99.5% uptime
- Graceful degradation
- Error recovery mechanisms

#### NFR-RL-02: Data Integrity

**Description**: System harus maintain data integrity  
**Requirements**:

- Database constraints
- Transaction management
- Data validation
- Regular backups

#### NFR-RL-03: Error Handling

**Description**: System harus handle errors gracefully  
**Requirements**:

- User-friendly error messages
- Error logging untuk debugging
- Fallback mechanisms
- Retry logic untuk failed operations

---

### 2.5 Maintainability

#### NFR-MT-01: Code Quality

**Description**: Codebase harus maintainable  
**Requirements**:

- Clean code principles
- TypeScript untuk type safety
- ESLint compliance
- Code documentation

#### NFR-MT-02: Testing

**Description**: System harus well-tested  
**Requirements**:

- Unit tests untuk critical functions
- Integration tests untuk API
- E2E tests untuk critical flows
- Test coverage > 70%

#### NFR-MT-03: Documentation

**Description**: System harus well-documented  
**Requirements**:

- API documentation
- Code comments untuk complex logic
- Wiki documentation
- README untuk setup

---

### 2.6 Compatibility

#### NFR-CP-01: Browser Support

**Description**: System harus work di modern browsers  
**Requirements**:

- Chrome (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Edge (last 2 versions)

#### NFR-CP-02: Device Support

**Description**: System harus work across devices  
**Requirements**:

- Desktop (1280px+)
- Tablet (768px - 1279px)
- Mobile (< 768px)
- Touch-friendly interfaces

---

## 3. System Constraints

### 3.1 Technical Constraints

- **Frontend**: Next.js 14+ dengan App Router
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Package Manager**: Bun
- **Backend API**: RESTful API
- **Authentication**: JWT-based

### 3.2 Business Constraints

- Must comply dengan university policies
- Data retention policies
- Budget limitations
- Timeline constraints

### 3.3 Regulatory Constraints

- Indonesian data protection laws
- Educational institution regulations
- Copyright dan intellectual property laws

---

## 4. Assumptions & Dependencies

### 4.1 Assumptions

- Users have stable internet connection
- Users have modern web browsers
- Backend API is always available
- Third-party services (Google OAuth) are reliable

### 4.2 Dependencies

- Backend API server
- Database server
- File storage service
- Email service untuk notifications
- Google OAuth service
- reCAPTCHA service

---

## 5. Acceptance Criteria

### General Criteria

- All functional requirements implemented
- Non-functional requirements met
- No critical bugs
- Security audit passed
- Performance benchmarks achieved
- User acceptance testing completed
- Documentation complete

---

[← Back: User Story Map](project-planning-User-Story-Map.md) | [Next: Software Vision (Full) →](software-vision.md)
