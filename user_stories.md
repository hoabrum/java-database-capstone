# User Story Template

**Title:**
_As a [user role], I want [feature/goal], so that [reason]._

**Acceptance Criteria:**
1. [Criteria 1]
2. [Criteria 2]
3. [Criteria 3]

**Priority:** [High/Medium/Low]
**Story Points:** [Estimated Effort in Points]
**Notes:**
- [Additional information or edge cases]

# User Stories

---

## Exercise 2: Admin User Stories

---

### User Story 1 — Admin Login

**Title:** Admin Login

*As an admin, I want to log into the portal with my username and password, so that I can manage the platform securely.*

**Acceptance Criteria:**

1. Admin can access a login page with username and password fields
2. System authenticates credentials against stored admin accounts
3. Invalid credentials display an appropriate error message without revealing which field is incorrect
4. After successful login, admin is redirected to the admin dashboard

**Priority:** High
**Story Points:** 3

**Notes:**

- Passwords must be stored as hashed values, never in plain text
- Consider implementing account lockout after a set number of failed attempts

---

### User Story 2 — Admin Logout

**Title:** Admin Logout

*As an admin, I want to log out of the portal, so that I can protect system access and prevent unauthorized use.*

**Acceptance Criteria:**

1. A logout button is visible on all admin pages
2. Clicking logout ends the current session and invalidates the session token
3. After logout, the admin is redirected to the login page
4. Pressing the browser back button after logout does not restore the authenticated session

**Priority:** High
**Story Points:** 2

**Notes:**

- Sessions should also expire automatically after a period of inactivity

---

### User Story 3 — Add Doctor to Portal

**Title:** Add Doctor to Portal

*As an admin, I want to add doctors to the portal, so that new doctors can be registered and available for patient appointments.*

**Acceptance Criteria:**

1. Admin can access an "Add Doctor" form from the dashboard
2. The form captures required fields such as name, specialty, email, and phone number
3. System validates all required fields before submission
4. A success confirmation message is displayed after the doctor is added
5. The new doctor appears in the doctors list immediately

**Priority:** High
**Story Points:** 5

**Notes:**

- Duplicate entries (e.g., same email) should be prevented
- Consider adding optional fields like profile photo or schedule availability

---

### User Story 4 — Delete Doctor Profile

**Title:** Delete Doctor Profile

*As an admin, I want to delete a doctor's profile from the portal, so that I can remove doctors who are no longer active on the platform.*

**Acceptance Criteria:**

1. Admin can select a doctor from the list and choose a delete action
2. A confirmation dialog appears before the deletion is executed
3. Upon confirmation, the doctor's profile is removed from the system
4. A success message is displayed after deletion
5. The deleted doctor no longer appears in search results or the doctors list

**Priority:** Medium
**Story Points:** 3

**Notes:**

- Consider soft-delete (marking as inactive) instead of hard-delete to preserve historical appointment data
- Existing future appointments tied to the doctor should be handled (cancelled or reassigned) before deletion

---

### User Story 5 — View Appointments Per Month Report

**Title:** View Appointments Per Month Report

*As an admin, I want to run a stored procedure in MySQL CLI to get the number of appointments per month, so that I can track platform usage statistics.*

**Acceptance Criteria:**

1. A stored procedure exists in the MySQL database that aggregates appointment counts by month
2. The admin can execute the procedure from the MySQL CLI using a simple CALL statement
3. The output displays month, year, and total number of appointments in a clear tabular format
4. The procedure handles months with zero appointments gracefully

**Priority:** Medium
**Story Points:** 3

**Notes:**

- Example usage: `CALL GetAppointmentsPerMonth();`
- Consider extending this in the future to display results within the admin dashboard UI rather than requiring CLI access
- Ensure the procedure accounts for date range filtering if needed

---

## Exercise 3: Patient User Stories

---

### User Story 1 — View Doctor List Without Login

**Title:** View Doctor List Without Login

*As a patient, I want to view a list of doctors without logging in, so that I can explore available options before deciding to register.*

**Acceptance Criteria:**

1. The doctor listing page is publicly accessible without authentication
2. Each doctor entry displays key information such as name, specialty, and availability
3. The list supports filtering or searching by specialty or doctor name
4. A prompt to sign up or log in is displayed when the patient attempts to book an appointment

**Priority:** High
**Story Points:** 3

**Notes:**

- Sensitive information (e.g., doctor's personal contact details) should not be exposed on the public listing
- Consider adding pagination if the doctor list is large

---

### User Story 2 — Patient Registration

**Title:** Patient Registration

*As a patient, I want to sign up using my email and password, so that I can create an account and book appointments.*

**Acceptance Criteria:**

1. A registration page is accessible from the homepage and login page
2. The form requires email, password, and basic profile details (name, phone number)
3. System validates that the email is not already registered
4. Passwords must meet minimum strength requirements (e.g., length, complexity)
5. A success message is displayed upon registration and the patient is redirected to the login page or dashboard

**Priority:** High
**Story Points:** 5

**Notes:**

- Consider adding email verification to confirm the patient's address
- Provide clear inline validation messages for each field

---

### User Story 3 — Patient Login

**Title:** Patient Login

*As a patient, I want to log into the portal, so that I can manage my bookings and access my account.*

**Acceptance Criteria:**

1. A login page with email and password fields is accessible to patients
2. Valid credentials grant access and redirect to the patient dashboard
3. Invalid credentials display a clear but secure error message
4. The session persists appropriately until the patient logs out or the session expires

**Priority:** High
**Story Points:** 3

**Notes:**

- Ensure the login page is visually distinct or role-separated from the admin login if they share the same URL
- Consider adding a "Forgot Password" link for future implementation

---

### User Story 4 — Patient Logout

**Title:** Patient Logout

*As a patient, I want to log out of the portal, so that I can secure my account and prevent unauthorized access.*

**Acceptance Criteria:**

1. A logout option is clearly visible on all authenticated patient pages
2. Clicking logout terminates the session and invalidates the token
3. The patient is redirected to the homepage or login page after logout
4. Navigating back via the browser does not restore the authenticated session

**Priority:** High
**Story Points:** 2

**Notes:**

- Idle sessions should time out automatically after a reasonable period of inactivity

---

### User Story 5 — Book an Appointment

**Title:** Book an Appointment

*As a patient, I want to log in and book an hour-long appointment, so that I can consult with a doctor at a scheduled time.*

**Acceptance Criteria:**

1. The patient can select a doctor from the available list after logging in
2. Available time slots are displayed based on the doctor's schedule
3. Each appointment is fixed at one hour in duration
4. The system prevents double-booking the same doctor for an overlapping time slot
5. A confirmation message with appointment details (doctor, date, time) is displayed after booking

**Priority:** High
**Story Points:** 8

**Notes:**

- Consider sending a confirmation email or notification upon successful booking
- Past dates and fully booked slots should not be selectable

---

### User Story 6 — View Upcoming Appointments

**Title:** View Upcoming Appointments

*As a patient, I want to view my upcoming appointments, so that I can prepare accordingly and keep track of my schedule.*

**Acceptance Criteria:**

1. The patient dashboard displays a list of all future appointments
2. Each entry shows the doctor's name, specialty, date, and time
3. Appointments are sorted in chronological order with the nearest date first
4. Past appointments are either hidden from this view or displayed in a separate section

**Priority:** Medium
**Story Points:** 3

**Notes:**

- Consider adding the ability to cancel or reschedule directly from this view in a future iteration
- If no upcoming appointments exist, display a friendly message with a link to book one

---

## Exercise 4: Doctor User Stories

---

### User Story 1 — Doctor Login

**Title:** Doctor Login

*As a doctor, I want to log into the portal, so that I can manage my appointments and access my schedule.*

**Acceptance Criteria:**

1. A login page with username/email and password fields is accessible to doctors
2. Valid credentials authenticate the doctor and redirect to the doctor dashboard
3. Invalid credentials display a secure error message without revealing which field is incorrect
4. The session remains active until the doctor logs out or it expires due to inactivity

**Priority:** High
**Story Points:** 3

**Notes:**

- Doctor accounts are created by the admin, so doctors do not self-register
- Ensure role-based access control so that a doctor session cannot access admin or patient-specific pages

---

### User Story 2 — Doctor Logout

**Title:** Doctor Logout

*As a doctor, I want to log out of the portal, so that I can protect my data and prevent unauthorized access to my account.*

**Acceptance Criteria:**

1. A logout option is visible and accessible on all authenticated doctor pages
2. Clicking logout ends the session and invalidates the session token
3. The doctor is redirected to the login page after logout
4. Using the browser back button after logout does not restore the authenticated session

**Priority:** High
**Story Points:** 2

**Notes:**

- Sessions should automatically expire after a defined period of inactivity as an additional safeguard

---

### User Story 3 — View Appointment Calendar

**Title:** View Appointment Calendar

*As a doctor, I want to view my appointment calendar, so that I can stay organized and manage my daily schedule effectively.*

**Acceptance Criteria:**

1. The doctor dashboard includes a calendar view showing all booked appointments
2. Each appointment entry displays the patient name, date, time, and duration
3. The calendar supports daily, weekly, and monthly views
4. Appointments are color-coded or visually distinguished from unavailable and free slots

**Priority:** High
**Story Points:** 5

**Notes:**

- Consider allowing the doctor to click on an appointment to see full details
- The calendar should load efficiently even with a large number of bookings

---

### User Story 4 — Mark Unavailability

**Title:** Mark Unavailability

*As a doctor, I want to mark my unavailability on specific dates or time slots, so that patients can only see and book available slots.*

**Acceptance Criteria:**

1. The doctor can select specific dates or time ranges and mark them as unavailable
2. Unavailable slots are immediately reflected and hidden from the patient booking view
3. The system prevents patients from booking appointments during marked unavailable periods
4. The doctor can remove or edit previously set unavailability to reopen slots
5. A confirmation message is displayed after the unavailability is saved

**Priority:** High
**Story Points:** 5

**Notes:**

- Consider supporting recurring unavailability (e.g., every Saturday) for future iterations
- If existing appointments conflict with newly marked unavailability, the doctor should be warned before confirming

---

### User Story 5 — Update Doctor Profile

**Title:** Update Doctor Profile

*As a doctor, I want to update my profile with specialization and contact information, so that patients have up-to-date and accurate information about me.*

**Acceptance Criteria:**

1. The doctor can access a profile editing page from the dashboard
2. Editable fields include specialization, phone number, email, and a brief bio
3. Changes are validated before saving (e.g., valid email format, non-empty required fields)
4. A success message is displayed after the profile is updated
5. Updated information is immediately reflected on the public doctor listing page

**Priority:** Medium
**Story Points:** 3

**Notes:**

- Certain fields such as name or doctor ID may be restricted to admin-only editing
- Consider allowing the doctor to upload or change a profile photo

---

### User Story 6 — View Patient Details for Upcoming Appointments

**Title:** View Patient Details for Upcoming Appointments

*As a doctor, I want to view patient details for my upcoming appointments, so that I can be prepared for each consultation.*

**Acceptance Criteria:**

1. The doctor can click on an upcoming appointment to view associated patient details
2. Patient details include name, contact information, and any notes submitted during booking
3. Only appointments assigned to the logged-in doctor are accessible
4. Patient information is displayed in a clear, readable format

**Priority:** Medium
**Story Points:** 3

**Notes:**

- Access to patient details must be restricted to the assigned doctor to maintain patient privacy
- Consider adding a section for the doctor to write pre-consultation notes in a future iteration
