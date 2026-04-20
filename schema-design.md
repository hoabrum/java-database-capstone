# Schema Design – Smart Clinic Management System

This document outlines the database schema design for the Smart Clinic Management System. It covers both **MySQL** (relational) for structured, transactional data and **MongoDB** (document-based) for flexible, semi-structured data.

---

## MySQL Database Design

MySQL is used for core operational data that requires strict structure, validation, referential integrity, and relational queries. The following tables represent the foundational entities and their relationships.

---

### Table: admin

Stores administrator accounts who manage the platform (add/remove doctors, view reports).

| Column       | Data Type    | Constraints                        |
|--------------|--------------|------------------------------------|
| id           | INT          | PRIMARY KEY, AUTO_INCREMENT        |
| username     | VARCHAR(50)  | NOT NULL, UNIQUE                   |
| password     | VARCHAR(255) | NOT NULL                           |
| full_name    | VARCHAR(100) | NOT NULL                           |
| email        | VARCHAR(100) | NOT NULL, UNIQUE                   |
| created_at   | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP          |

**Design notes:**
- Password column is VARCHAR(255) to accommodate hashed values (e.g., BCrypt output).
- Username and email are both UNIQUE to prevent duplicate admin accounts.

---

### Table: doctors

Stores doctor profiles including their specialization and contact details.

| Column          | Data Type    | Constraints                        |
|-----------------|--------------|------------------------------------|
| id              | INT          | PRIMARY KEY, AUTO_INCREMENT        |
| full_name       | VARCHAR(100) | NOT NULL                           |
| email           | VARCHAR(100) | NOT NULL, UNIQUE                   |
| phone           | VARCHAR(20)  | NULL                               |
| specialization  | VARCHAR(100) | NOT NULL                           |
| bio             | TEXT         | NULL                               |
| is_active       | BOOLEAN      | DEFAULT TRUE                       |
| created_at      | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP          |
| updated_at      | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP |

**Design notes:**
- `is_active` supports soft-delete so that historical appointment data is preserved when an admin removes a doctor.
- `bio` is optional and allows the doctor to describe their experience.
- Email format validation is handled at the application layer.

---

### Table: patients

Stores patient accounts who register themselves to book appointments.

| Column       | Data Type    | Constraints                        |
|--------------|--------------|------------------------------------|
| id           | INT          | PRIMARY KEY, AUTO_INCREMENT        |
| full_name    | VARCHAR(100) | NOT NULL                           |
| email        | VARCHAR(100) | NOT NULL, UNIQUE                   |
| password     | VARCHAR(255) | NOT NULL                           |
| phone        | VARCHAR(20)  | NULL                               |
| date_of_birth| DATE         | NULL                               |
| gender       | ENUM('Male','Female','Other') | NULL                |
| created_at   | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP          |

**Design notes:**
- Patients self-register, so a password field is required.
- `date_of_birth` and `gender` are optional at registration but useful for clinical context.
- Email uniqueness prevents duplicate patient accounts.

---

### Table: appointments

Tracks scheduled appointments between patients and doctors. This is the central transactional table.

| Column           | Data Type | Constraints                                    |
|------------------|-----------|------------------------------------------------|
| id               | INT       | PRIMARY KEY, AUTO_INCREMENT                    |
| doctor_id        | INT       | NOT NULL, FOREIGN KEY → doctors(id)            |
| patient_id       | INT       | NOT NULL, FOREIGN KEY → patients(id)           |
| appointment_date | DATE      | NOT NULL                                       |
| appointment_time | TIME      | NOT NULL                                       |
| duration_minutes | INT       | NOT NULL, DEFAULT 60                           |
| status           | ENUM('Scheduled','Completed','Cancelled') | DEFAULT 'Scheduled' |
| created_at       | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP                      |

**Design notes:**
- `duration_minutes` defaults to 60 (one-hour appointments) as defined in the user stories.
- `status` uses ENUM for clarity and data integrity instead of integer codes.
- Foreign keys reference `doctors(id)` and `patients(id)`. ON DELETE is set to RESTRICT to prevent accidental removal of doctors or patients who have appointment history.
- Appointment date and time are separated to simplify queries like "show all appointments on a given date."
- A UNIQUE constraint on `(doctor_id, appointment_date, appointment_time)` can be added to prevent double-booking.

---

### Table: doctor_availability

Tracks the time slots when a doctor is available or unavailable for appointments.

| Column       | Data Type | Constraints                              |
|--------------|-----------|------------------------------------------|
| id           | INT       | PRIMARY KEY, AUTO_INCREMENT              |
| doctor_id    | INT       | NOT NULL, FOREIGN KEY → doctors(id)      |
| day_of_week  | ENUM('Mon','Tue','Wed','Thu','Fri','Sat','Sun') | NOT NULL |
| start_time   | TIME      | NOT NULL                                 |
| end_time     | TIME      | NOT NULL                                 |
| is_available | BOOLEAN   | DEFAULT TRUE                             |

**Design notes:**
- This table defines the doctor's recurring weekly schedule.
- `is_available = FALSE` allows doctors to mark specific days/slots as unavailable.
- The patient booking system should query this table to display only open slots.
- For one-off unavailability (e.g., vacation), a separate `doctor_unavailability` table with specific dates could be added in a future iteration.

---

### Table: doctor_unavailability

Handles specific date-based unavailability (holidays, leave, personal days).

| Column       | Data Type | Constraints                              |
|--------------|-----------|------------------------------------------|
| id           | INT       | PRIMARY KEY, AUTO_INCREMENT              |
| doctor_id    | INT       | NOT NULL, FOREIGN KEY → doctors(id)      |
| unavail_date | DATE      | NOT NULL                                 |
| reason       | VARCHAR(255) | NULL                                  |

**Design notes:**
- Complements `doctor_availability` by handling exceptions to the regular schedule.
- The booking system checks both tables: recurring availability AND specific-date unavailability.

---

### Entity Relationship Summary

```
admin          (standalone – manages the platform)

doctors  ──<  appointments  >──  patients
   │
   └──<  doctor_availability
   │
   └──<  doctor_unavailability
```

- A **doctor** has many **appointments** and many **availability** slots.
- A **patient** has many **appointments**.
- An **appointment** belongs to exactly one doctor and one patient.

---

## MongoDB Collection Design

MongoDB is used for data that is semi-structured, may evolve over time, or benefits from flexible schemas with nested objects and arrays. The following collections complement the MySQL schema above.

---

### Collection: prescriptions

Prescriptions are tied to a specific appointment but can contain variable fields (different medications, dosages, pharmacy details). MongoDB's flexible schema is ideal here.

```json
{
  "_id": "ObjectId('664abc123456def789012345')",
  "appointmentId": 51,
  "doctorId": 7,
  "doctorName": "Dr. Sarah Lee",
  "patientId": 14,
  "patientName": "John Smith",
  "datePrescribed": "2026-04-20T10:30:00Z",
  "medications": [
    {
      "name": "Paracetamol",
      "dosage": "500mg",
      "frequency": "Every 6 hours",
      "duration": "5 days",
      "instructions": "Take after meals"
    },
    {
      "name": "Amoxicillin",
      "dosage": "250mg",
      "frequency": "Every 8 hours",
      "duration": "7 days",
      "instructions": "Complete the full course even if symptoms improve"
    }
  ],
  "refillCount": 1,
  "pharmacy": {
    "name": "HealthPlus Pharmacy",
    "location": "123 Main Street",
    "phone": "+84-24-1234-5678"
  },
  "doctorNotes": "Patient presents with mild bacterial infection. Follow up in one week if symptoms persist.",
  "tags": ["antibiotic", "follow-up-required"]
}
```

**Design notes:**
- `medications` is an array of embedded documents, allowing a single prescription to include multiple drugs with varying instructions — this would require a separate join table in MySQL.
- `pharmacy` is a nested object since it's contextual to this prescription and doesn't need its own relational table.
- `tags` array enables easy categorization and filtering.
- `appointmentId`, `doctorId`, and `patientId` reference the MySQL records by ID, keeping the two databases loosely coupled.
- Names are denormalized (stored alongside IDs) to allow MongoDB queries to return human-readable results without cross-database joins.

---

### Collection: feedback

Patient feedback after an appointment is free-form and optional — a natural fit for MongoDB.

```json
{
  "_id": "ObjectId('664def789012abc345678901')",
  "appointmentId": 51,
  "patientId": 14,
  "patientName": "John Smith",
  "doctorId": 7,
  "doctorName": "Dr. Sarah Lee",
  "rating": 5,
  "comments": "Dr. Lee was very thorough and took the time to explain everything clearly.",
  "categories": ["professionalism", "communication", "punctuality"],
  "submittedAt": "2026-04-20T15:00:00Z",
  "isAnonymous": false
}
```

**Design notes:**
- `rating` is a simple numeric score (1–5).
- `categories` is an array that allows tagging feedback by aspect, useful for analytics dashboards.
- `isAnonymous` lets patients choose to hide their identity from the doctor view.
- Feedback structure may evolve (e.g., adding follow-up questions), so a flexible schema avoids schema migrations.

---

### Collection: logs

System activity logs capture events like check-ins, messages, and account actions. These are append-heavy and schema-variable.

```json
{
  "_id": "ObjectId('664aaa111222bbb333444555')",
  "eventType": "PATIENT_CHECK_IN",
  "userId": 14,
  "userRole": "patient",
  "description": "Patient checked in for appointment #51",
  "metadata": {
    "appointmentId": 51,
    "doctorId": 7,
    "checkInMethod": "online"
  },
  "ipAddress": "192.168.1.42",
  "timestamp": "2026-04-20T09:55:00Z"
}
```

**Design notes:**
- `eventType` acts as a discriminator for different log categories (check-in, login, logout, profile update, etc.).
- `metadata` is a flexible nested object whose shape varies depending on the event type — this would be difficult to model in a fixed MySQL table.
- Logs are write-heavy and rarely updated, making MongoDB's append-optimized storage a good fit.
- Useful for auditing, analytics, and debugging.

---

## Design Decision Summary

| Data                    | Database | Reason                                                        |
|-------------------------|----------|---------------------------------------------------------------|
| Admin accounts          | MySQL    | Fixed structure, authentication, role management              |
| Doctor profiles         | MySQL    | Relational (linked to appointments, availability)             |
| Patient accounts        | MySQL    | Relational (linked to appointments), login credentials        |
| Appointments            | MySQL    | Transactional, requires foreign keys and integrity checks     |
| Doctor availability     | MySQL    | Structured schedule data with relational constraints          |
| Prescriptions           | MongoDB  | Variable medication lists, nested pharmacy info, evolving schema |
| Patient feedback        | MongoDB  | Free-form text, optional fields, flexible categorization      |
| Activity logs           | MongoDB  | High write volume, variable metadata, append-only pattern     |
