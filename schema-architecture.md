Section 1: Architecture summary

This Spring Boot application uses both MVC and REST controllers.
Thymeleaf templates are used for the Admin and Doctor dashboards,
while REST APIs serve all other modules. The application interacts with two databases—MySQL (for patient, doctor, appointment, and admin data) and MongoDB (for prescriptions).
All controllers route requests through a common service layer, which in turn delegates to the appropriate repositories.
MySQL uses JPA entities while MongoDB uses document models.

Section 2: Numbered flow of data and control
1. User accesses AdminDashboard or Appointment pages.
2. The action is routed to the appropriate Thymeleaf or REST controller.
3. The controller calls the service layer
4. The service calls the data layer (repositories) to perform CRUD operations
5. The repository accesses MySQL database
6. The MySQL Model interacts with MySQL database throught JPA Entity
7. The MySQL Model transfers data from database to JPA Entity
