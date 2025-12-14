# Use Case Diagram - Bengkel Koding Web V2

## Overview

Use Case Diagram menggambarkan interaksi antara aktor (users) dengan sistem dan fungsionalitas yang tersedia. Dokumen ini berisi detail lengkap use cases untuk semua role dalam platform pembelajaran Bengkel Koding.

Setiap use case menjelaskan:

- **Actor** - Siapa yang melakukan aksi
- **Goal** - Apa yang ingin dicapai
- **Preconditions** - Kondisi sebelum use case dijalankan
- **Postconditions** - Kondisi setelah use case selesai
- **Main Flow** - Alur normal eksekusi
- **Alternative Flow** - Alur alternatif/exception

---

## Dokumentasi Lengkap

Untuk melihat detail lengkap Use Case yang mencakup:

- **Student Use Cases** - Onboarding, course discovery, learning experience, attendance, dan certification
- **Asisten Use Cases** - Class management, grading & feedback, dan attendance management
- **Dosen Use Cases** - Course creation, class administration, assessment, dan student monitoring
- **Admin Use Cases** - Content management, user management, dan analytics
- **Super Admin Use Cases** - System administration dan access control
- **Use Case Diagrams** - Visual diagrams untuk setiap modul
- **Priority Matrix** - Implementation priorities dan MVP features

**Akses dokumentasi lengkap di:**

📊 **[Use Case Documentation - Google Sheets](https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID/edit)**

> **Catatan:** Spreadsheet berisi detail flow, preconditions, postconditions, dan business rules untuk setiap use case.

---

## Struktur Use Case

### Format Use Case

```
UC-[ROLE][NUMBER]: [Use Case Name]

Actor: [Role Name]
Description: [Brief description of what this use case does]

Preconditions:
- Condition 1
- Condition 2

Main Flow:
1. Step 1
2. Step 2
3. Step 3

Alternative Flow:
- Alt 1: [Alternative scenario]
- Alt 2: [Exception handling]

Postconditions:
- Expected outcome 1
- Expected outcome 2
```

### Actor/Role Codes

- **S** - Student (Mahasiswa)
- **A** - Asisten (Teaching Assistant)
- **D** - Dosen (Lecturer)
- **AD** - Admin
- **SA** - Super Admin

---

## Quick Reference

### Priority Levels

| Level  | Description                     | Examples                        |
| ------ | ------------------------------- | ------------------------------- |
| High   | Critical for MVP, must have     | Login, Register, Course Catalog |
| Medium | Important features, should have | QR Attendance, Notifications    |
| Low    | Nice to have, could have        | Advanced Reports, Analytics     |

### Use Case Status

- 🔵 **Not Started** - Belum dikerjakan
- 🟡 **In Progress** - Sedang dikembangkan
- 🟢 **Completed** - Sudah selesai
- 🔴 **Blocked** - Ada blocker/dependency

### Implementation Phases

1. **Phase 1 (MVP)** - Core authentication, course browsing, basic enrollment
2. **Phase 2** - Assignment submission, grading system, attendance
3. **Phase 3** - Certificate generation, advanced analytics, learning paths
4. **Phase 4** - Optimization, additional features, enhancements

---

## Actors Overview

### Primary Actors (Direct Users)

| Actor       | Description                                  | Primary Goals                                 |
| ----------- | -------------------------------------------- | --------------------------------------------- |
| Student     | Mahasiswa yang mengikuti course              | Learn, submit assignments, earn certificates  |
| Asisten     | Teaching assistant yang membantu dosen       | Grade assignments, manage attendance          |
| Dosen       | Lecturer yang membuat dan mengajar course    | Create courses, monitor students, issue certs |
| Admin       | Administrator yang mengelola konten platform | Manage content, users, generate reports       |
| Super Admin | System administrator dengan akses penuh      | System configuration, full access control     |

### Secondary Actors (Supporting Systems)

- **Email Service** - Untuk notifikasi dan verifikasi
- **Storage Service** - Untuk menyimpan file dan media
- **Authentication Service** - OAuth providers (Google, etc.)
- **Certificate Service** - Generate dan verify certificates

---

## Related Documentation

- [← Back: UI/UX Standards](development-guide-UI-UX-Standards)
- [Next: Requirement Specification →](Requirement-Specification)
- [Database Schema](development-guide-Database-Schema)
- [Technical Architectures](getting-started-Technical-Architectures)

---

## Contributing

Untuk menambah atau mengubah use cases:

1. Update dokumentasi di Google Sheets
2. Review dengan team dan stakeholders
3. Update use case diagram jika ada perubahan struktur
4. Link use case ke requirement specification
5. Update wiki jika ada perubahan mayor

---

**Last Updated:** November 4, 2025

---

[← Back: UI/UX Standards](development-guide-UI-UX-Standards.md) | [Next: Requirement Specification →](Requirement-Specification.md)
