# Student Information System Data Models

## Overview

This document defines the data models and relationships for the Student Information System. The design follows a relational database structure optimized for PostgreSQL while maintaining flexibility for future extensions.

## Entity Relationship Overview

```
Person (Base Entity)
├── Student
├── Parent/Guardian
└── Staff (Teacher/Admin)

Academic Structure
├── Academic Year
├── Course
├── Class Section
└── Enrollment

Records & Tracking
├── Attendance
├── Grade
├── Assignment
└── Document

Communication
├── Message
├── Announcement
└── Notification
```

## Core Entities

### Person (Base Entity)
The Person entity serves as the base for all individuals in the system.

```typescript
interface Person {
  id: string; // UUID
  first_name: string;
  last_name: string;
  middle_name?: string;
  preferred_name?: string;
  date_of_birth: Date;
  gender: 'male' | 'female' | 'other' | 'prefer_not_to_say';
  photo_url?: string;
  created_at: Date;
  updated_at: Date;
  deleted_at?: Date; // Soft delete
}
```

### Student
Extends Person entity with student-specific information.

```typescript
interface Student {
  person_id: string; // FK to Person
  student_id: string; // School-assigned ID
  grade_level: string; // K, 1-12
  enrollment_status: 'active' | 'inactive' | 'graduated' | 'withdrawn';
  enrollment_date: Date;
  graduation_date?: Date;
  previous_schools?: {
    school_name: string;
    city: string;
    state: string;
    years_attended: string;
  }[];
  special_needs?: string;
  health_conditions?: string;
  medications?: string;
  allergies?: string;
  dietary_restrictions?: string;
  notes?: string;
}
```

### Parent/Guardian
Extends Person entity with parent-specific information.

```typescript
interface Parent {
  person_id: string; // FK to Person
  relationship_type: 'mother' | 'father' | 'stepmother' | 'stepfather' | 
                     'guardian' | 'grandparent' | 'other';
  occupation?: string;
  employer?: string;
  work_hours?: string;
  preferred_contact_time?: string;
  preferred_contact_method: 'phone' | 'email' | 'text';
  speaks_english: boolean;
  preferred_language?: string;
  is_primary_contact: boolean;
  has_legal_custody: boolean;
  has_physical_custody: boolean;
  can_pickup_child: boolean;
  restraining_order: boolean;
  court_orders_notes?: string;
}
```

### Staff
Extends Person entity with staff-specific information.

```typescript
interface Staff {
  person_id: string; // FK to Person
  employee_id: string;
  role: 'teacher' | 'admin' | 'principal' | 'counselor' | 'nurse' | 
        'custodian' | 'cafeteria' | 'aide' | 'other';
  hire_date: Date;
  employment_status: 'active' | 'inactive' | 'terminated' | 'on_leave';
  department?: string;
  certifications?: {
    name: string;
    issuer: string;
    issue_date: Date;
    expiry_date?: Date;
  }[];
  education?: {
    degree: string;
    institution: string;
    graduation_year: number;
  }[];
  emergency_contact: {
    name: string;
    relationship: string;
    phone: string;
    alternate_phone?: string;
  };
  background_check_date?: Date;
  fingerprint_clearance_date?: Date;
}
```

## Contact Information

### Contact
Multiple contact methods per person.

```typescript
interface Contact {
  id: string;
  person_id: string; // FK to Person
  type: 'phone_home' | 'phone_work' | 'phone_mobile' | 'email' | 'fax';
  value: string;
  is_primary: boolean;
  can_receive_texts: boolean; // For phone numbers
  notes?: string;
}
```

### Address
Multiple addresses per person.

```typescript
interface Address {
  id: string;
  person_id: string; // FK to Person
  type: 'home' | 'work' | 'mailing' | 'other';
  street_address_1: string;
  street_address_2?: string;
  city: string;
  state_province: string;
  postal_code: string;
  country: string;
  is_primary: boolean;
  verified_date?: Date;
}
```

## Academic Structure

### AcademicYear
Defines school year periods.

```typescript
interface AcademicYear {
  id: string;
  name: string; // e.g., "2024-2025"
  start_date: Date;
  end_date: Date;
  is_current: boolean;
  terms: {
    name: string; // "Fall Semester", "Q1", etc.
    start_date: Date;
    end_date: Date;
    is_current: boolean;
  }[];
  holidays: {
    name: string;
    date: Date;
    recurring_annually: boolean;
  }[];
}
```

### Course
Master course catalog.

```typescript
interface Course {
  id: string;
  code: string; // e.g., "MATH101"
  name: string; // e.g., "Algebra I"
  description?: string;
  department: string;
  grade_levels: string[]; // ["9", "10"]
  prerequisites?: string[]; // Course IDs
  credits?: number;
  is_required: boolean;
  is_honors: boolean;
  is_ap: boolean;
  max_students: number;
  min_students: number;
}
```

### ClassSection
Specific instance of a course.

```typescript
interface ClassSection {
  id: string;
  course_id: string; // FK to Course
  academic_year_id: string; // FK to AcademicYear
  term: string; // Term name from AcademicYear
  section_number: string; // e.g., "01", "02"
  teacher_id: string; // FK to Staff
  assistant_teacher_id?: string; // FK to Staff
  room?: string;
  schedule: {
    day: 'monday' | 'tuesday' | 'wednesday' | 'thursday' | 'friday';
    start_time: string; // "09:00"
    end_time: string; // "09:50"
    type: 'regular' | 'lab' | 'study_hall';
  }[];
  max_students: number;
  current_enrollment: number;
  is_active: boolean;
}
```

### Enrollment
Links students to class sections.

```typescript
interface Enrollment {
  id: string;
  student_id: string; // FK to Student
  class_section_id: string; // FK to ClassSection
  enrollment_date: Date;
  withdrawal_date?: Date;
  status: 'enrolled' | 'completed' | 'withdrawn' | 'failed' | 'incomplete';
  final_grade?: string;
  final_percentage?: number;
  credits_earned?: number;
  grade_level_at_enrollment: string;
  notes?: string;
}
```

## Relationships

### StudentParent
Many-to-many relationship between students and parents.

```typescript
interface StudentParent {
  student_id: string; // FK to Student
  parent_id: string; // FK to Parent
  relationship_type: string;
  lives_with_student: boolean;
  is_emergency_contact: boolean;
  emergency_contact_priority: number; // 1, 2, 3, etc.
  can_access_records: boolean;
  can_make_decisions: boolean;
}
```

## Records & Tracking

### Attendance
Daily or period-based attendance.

```typescript
interface Attendance {
  id: string;
  student_id: string; // FK to Student
  date: Date;
  type: 'daily' | 'period';
  class_section_id?: string; // FK to ClassSection (for period attendance)
  status: 'present' | 'absent' | 'tardy' | 'excused_absence' | 
          'unexcused_absence' | 'suspended' | 'field_trip';
  arrival_time?: string;
  departure_time?: string;
  minutes_missed?: number;
  reason?: string;
  notes?: string;
  recorded_by: string; // FK to Staff
  recorded_at: Date;
  parent_notified: boolean;
  parent_notified_at?: Date;
}
```

### Assignment
Assignments, tests, quizzes, etc.

```typescript
interface Assignment {
  id: string;
  class_section_id: string; // FK to ClassSection
  name: string;
  description?: string;
  type: 'homework' | 'quiz' | 'test' | 'project' | 'participation' | 
        'final_exam' | 'midterm' | 'other';
  category: string; // For weighted grades
  points_possible: number;
  weight?: number; // For weighted grading
  due_date: Date;
  assigned_date: Date;
  is_extra_credit: boolean;
  allow_late_submission: boolean;
  late_penalty_per_day?: number;
  instructions?: string;
  attachments?: {
    name: string;
    url: string;
    type: string;
  }[];
}
```

### Grade
Student grades for assignments.

```typescript
interface Grade {
  id: string;
  enrollment_id: string; // FK to Enrollment
  assignment_id?: string; // FK to Assignment
  type: 'assignment' | 'participation' | 'behavior' | 'final' | 'midterm';
  points_earned?: number;
  points_possible: number;
  percentage?: number;
  letter_grade?: string;
  is_missing: boolean;
  is_late: boolean;
  is_excused: boolean;
  graded_date?: Date;
  graded_by: string; // FK to Staff
  comments?: string;
  last_modified: Date;
  modified_by: string; // FK to Staff
}
```

### Document
Student documents and files.

```typescript
interface Document {
  id: string;
  owner_type: 'student' | 'staff' | 'general';
  owner_id: string; // FK to Student or Staff
  category: 'enrollment' | 'medical' | 'legal' | 'academic' | 
            'disciplinary' | 'financial' | 'other';
  type: string; // "Birth Certificate", "IEP", etc.
  name: string;
  file_url: string;
  file_size: number;
  file_type: string;
  uploaded_date: Date;
  uploaded_by: string; // FK to Staff
  expiry_date?: Date;
  is_verified: boolean;
  verified_by?: string; // FK to Staff
  verified_date?: Date;
  is_confidential: boolean;
  access_roles: string[]; // Which roles can view
  notes?: string;
}
```

## Communication

### Message
Direct messages between users.

```typescript
interface Message {
  id: string;
  sender_id: string; // FK to Person
  sender_type: 'staff' | 'parent' | 'student';
  recipient_id: string; // FK to Person
  recipient_type: 'staff' | 'parent' | 'student';
  subject: string;
  body: string;
  sent_at: Date;
  read_at?: Date;
  is_urgent: boolean;
  requires_response: boolean;
  response_due_date?: Date;
  thread_id?: string; // For conversation threading
  attachments?: {
    name: string;
    url: string;
    type: string;
  }[];
}
```

### Announcement
School-wide or targeted announcements.

```typescript
interface Announcement {
  id: string;
  title: string;
  content: string;
  author_id: string; // FK to Staff
  target_audience: 'all' | 'students' | 'parents' | 'staff' | 'specific';
  target_grade_levels?: string[];
  target_class_sections?: string[]; // FK to ClassSection
  publish_date: Date;
  expiry_date?: Date;
  is_pinned: boolean;
  is_urgent: boolean;
  requires_acknowledgment: boolean;
  attachments?: {
    name: string;
    url: string;
    type: string;
  }[];
  acknowledgments?: {
    person_id: string;
    acknowledged_at: Date;
  }[];
}
```

### Notification
System notifications and alerts.

```typescript
interface Notification {
  id: string;
  recipient_id: string; // FK to Person
  type: 'grade_posted' | 'attendance_marked' | 'assignment_due' | 
        'message_received' | 'announcement' | 'schedule_change' | 
        'payment_due' | 'system' | 'emergency';
  title: string;
  body: string;
  data?: any; // Additional context data
  created_at: Date;
  read_at?: Date;
  delivered_at?: Date;
  delivery_method: 'in_app' | 'email' | 'sms' | 'push';
  priority: 'low' | 'normal' | 'high' | 'urgent';
  action_url?: string; // Deep link to relevant content
}
```

## Audit & Security

### AuditLog
Track all data changes for compliance.

```typescript
interface AuditLog {
  id: string;
  user_id: string; // FK to Person
  user_type: 'staff' | 'parent' | 'student';
  action: 'create' | 'read' | 'update' | 'delete' | 'login' | 'logout';
  entity_type: string; // Table name
  entity_id: string; // Record ID
  changes?: {
    field: string;
    old_value: any;
    new_value: any;
  }[];
  ip_address: string;
  user_agent: string;
  timestamp: Date;
  session_id: string;
}
```

### UserSession
Active user sessions.

```typescript
interface UserSession {
  id: string;
  user_id: string; // FK to Person
  token: string;
  refresh_token: string;
  device_info: {
    type: 'web' | 'ios' | 'android';
    name: string;
    os_version: string;
    app_version: string;
  };
  ip_address: string;
  created_at: Date;
  last_activity: Date;
  expires_at: Date;
  is_active: boolean;
}
```

## Database Indexes

### Performance Indexes
```sql
-- Person searches
CREATE INDEX idx_person_name ON person(last_name, first_name);
CREATE INDEX idx_person_dob ON person(date_of_birth);

-- Student queries
CREATE INDEX idx_student_status ON student(enrollment_status);
CREATE INDEX idx_student_grade ON student(grade_level);

-- Attendance queries
CREATE INDEX idx_attendance_date ON attendance(date, student_id);
CREATE INDEX idx_attendance_student ON attendance(student_id, date DESC);

-- Grade queries
CREATE INDEX idx_grade_enrollment ON grade(enrollment_id);
CREATE INDEX idx_grade_assignment ON grade(assignment_id);

-- Enrollment queries
CREATE INDEX idx_enrollment_student ON enrollment(student_id, status);
CREATE INDEX idx_enrollment_section ON enrollment(class_section_id);
```

### Unique Constraints
```sql
-- Ensure unique identifiers
ALTER TABLE student ADD CONSTRAINT uk_student_id UNIQUE (student_id);
ALTER TABLE staff ADD CONSTRAINT uk_employee_id UNIQUE (employee_id);

-- Prevent duplicate enrollments
ALTER TABLE enrollment ADD CONSTRAINT uk_enrollment 
  UNIQUE (student_id, class_section_id);

-- Ensure one primary contact per person
ALTER TABLE contact ADD CONSTRAINT uk_primary_contact 
  UNIQUE (person_id, type) WHERE is_primary = true;
```

## Data Migration Considerations

### Import Formats
- CSV templates for bulk student import
- Excel templates with validation
- Google Sheets integration
- Previous SIS data mapping

### Export Formats
- Student transcripts (PDF)
- Grade reports (PDF/Excel)
- Attendance reports (CSV/Excel)
- Full data export (JSON/SQL)

### Archive Strategy
- Graduated student records
- Historical grade data
- Attendance history
- Document retention policies