This is a comprehensive technical summary of the Viyabari — Fish Trade Management System. You can save this text as a .md or .doc file for your records.
Viyabari: Project Documentation & Technical Summary
1. Project Overview
Viyabari (meaning "Business" in some South Asian languages) is a specialized Fish Trade Management System (FTMS). It is designed as an end-to-end monolithic Spring Boot application to manage the complexities of the fish industry, including procurement from boat owners, quality control, cold storage inventory management, local/export sales, and financial tracking.
2. Technical Stack
The project leverages the Spring Boot 3.2.x ecosystem with a focus on modern development practices:
Technology
Purpose
Java 17
Core programming language.
Spring Boot 3.2
Application framework (Web, Security, Data JPA).
MySQL 8.0
Relational database for persistent storage.
Thymeleaf
Server-side template engine for HTML rendering.
Spring Data JPA
Abstraction layer over Hibernate for database operations.
Spring Security
Authentication and Role-Based Access Control (RBAC).
Flyway
Version control for database schema (Migrations).
Lombok
Reduces boilerplate code (Getters, Setters, Constructors).
MapStruct
High-performance bean mapping (Entity ↔ DTO).
Bootstrap 5
Frontend styling and responsive layout.
3. Core Features & Functional Modules
A. Procurement & Master Data
•
Supplier Management: Tracks boat owners, auctioneers, and traders.
•
Boat Registry: Links boats to specific owners.
•
Fish Catalog: Manages fish species and categories (Freshwater, Seafood, etc.).
B. Sales & Invoicing
•
Dynamic Invoicing: Create multi-item invoices for customers.
•
Automated Calculation: System calculates line-item totals and grand totals.
•
Invoice Numbering: Auto-generates unique formatted IDs (e.g., INV-2026-0001).
C. Logistics & Cold Storage
•
Storage Tracking: Real-time load monitoring of cold storage rooms.
•
Capacity Alerts: Visual indicators (Red/Green) based on storage capacity usage.
•
Delivery Management: Assigning vehicles and drivers to sales invoices for delivery tracking.
D. Financials & Reporting
•
Cash Flow: Tracks payments received from customers and payments made to suppliers.
•
Profit & Loss (P&L): Real-time calculation of Gross and Net Profit.
•
Inventory Reports: Snapshot of current stock levels across all species.
E. Security (RBAC)
•
Multi-Role System:
◦
ADMIN: Full access to all modules.
◦
BUYER: Restricted to procurement and fish catalog.
◦
WAREHOUSE: Restricted to cold storage and transport.
4. Architectural Pattern: "Package-by-Feature"
Unlike traditional "Package-by-Layer" (where all controllers are in one folder), Viyabari groups code by business feature. For example, the com.fishtrade.sales package contains the Controller, Service, Repository, Entity, and DTOs specifically for Sales.
The Layered Flow:
1.
Web/Controller Layer: Handles HTTP requests and returns Thymeleaf templates.
2.
Service Layer: Contains the "Business Brain" (calculations, validation, transaction management).
3.
Repository Layer: Uses Spring Data JPA to talk to the MySQL database.
4.
Entity Layer: Java classes that represent the database tables.
5. Key Annotations & How They Work
Spring Core & Web
•
@SpringBootApplication: The entry point. It triggers auto-configuration and component scanning.
•
@Controller: Marks a class as a web handler that returns HTML views (Thymeleaf).
•
@Service: Marks a class as the business logic layer.
•
@Repository: Marks a class for data access logic.
•
@Configuration: Defines a class that provides bean definitions (like Security).
Data JPA (Database)
•
@Entity: Tells JPA that this Java class is a database table.
•
@Table(name=...): Specifies the exact name of the table in MySQL.
•
@Id & @GeneratedValue: Defines the Primary Key and tells MySQL to auto-increment it.
•
@ManyToOne / @OneToMany: Defines relationships between tables (e.g., Many Boats belong to One Supplier).
•
@JoinColumn: Specifies the Foreign Key column.
•
@Enumerated(EnumType.STRING): Saves Enum values as text (e.g., "ADMIN") instead of numbers.
Lombok (Boilerplate)
•
@Getter / @Setter: Automatically creates get...() and set...() methods.
•
@NoArgsConstructor: Creates a default empty constructor required by JPA.
•
@RequiredArgsConstructor: Creates a constructor for all final fields (used for Dependency Injection).
Security & Validation
•
@EnableWebSecurity: Enables Spring Security's web protection features.
•
@sec:authorize (Thymeleaf): Hides or shows HTML elements based on the user's role.
•
@Valid / @NotBlank / @NotNull: Automatically checks form data before saving to ensure no empty fields or invalid numbers.
6. End-to-End Workflow Example: A Sale
1.
Input: A user logs in as ADMIN and goes to the "Create Sale" form.
2.
Validation: The user selects a Customer and adds Fish. @Valid ensures a quantity is entered.
3.
Service Processing: The SalesService generates an invoice number and calculates the totalAmount.
4.
Persistence: The SalesRepository saves the data to the sales_master and sales_items tables.
5.
Logistics Integration: The user goes to "Transport" and assigns a vehicle to that specific Invoice.
6.
Reporting: The "Business Dashboard" immediately reflects the new sale in the Total Revenue and P&L charts.
Conclusion
Viyabari is a robust, scalable starter for a trade management system. By using Flyway for migrations and Spring Security for protection, it ensures that the data is structured and secure, while the Thymeleaf + Bootstrap combination provides a professional and responsive user experience.




This is a detailed study guide for your project, Viyabari. I have broken it down into sections to help you understand the "Why" and "How" behind every technical choice.
📘 Viyabari Project Study Guide
1. Project Concept
Viyabari is a Monolithic Web Application designed to manage the supply chain of a fish trading business.
•
Input Side: Buying fish from suppliers/boats.
•
Storage Side: Tracking stock in cold storage rooms.
•
Output Side: Selling fish to local/international customers.
•
Management Side: Tracking money (Finance) and calculating profit/loss (Reports).
2. Technical Stack (The "How")
Backend: Spring Boot 3.2.x
•
How: It acts as the "Engine." It provides Dependency Injection (connecting different classes automatically) and Inversion of Control (the framework manages the application lifecycle).
•
Key Tool: Spring Data JPA was used to write database queries using Java methods instead of complex SQL.
Frontend: Thymeleaf & Bootstrap 5
•
How: Unlike React or Angular, Thymeleaf is a Server-Side Template Engine. This means the HTML is prepared on the server and sent to the browser already filled with data.
•
Bootstrap provides the CSS grid and components (cards, tables, buttons) to make the UI look professional and mobile-responsive.
Database: MySQL & Flyway
•
How: MySQL stores the actual data. Flyway is the "Version Control" for the database. Instead of manually creating tables, we wrote .sql scripts in db/migration. Flyway runs these scripts automatically to ensure the database structure is always correct.
Helper Tools: Lombok & MapStruct
•
Lombok: Used to hide "junk" code. Instead of writing 50 lines of Getters and Setters, we use one annotation.
•
MapStruct: Used for DTO (Data Transfer Object) Pattern. We never show the database "Entity" directly to the user for security. MapStruct automatically copies data from an Entity to a DTO.
3. Core Architecture: Layered & Feature-Based
We followed a Feature-Based structure, meaning everything related to "Sales" is in one folder. Within each folder, we follow these layers:
1.
Entity Layer: The blueprint of the database table.
2.
Repository Layer: The gateway to the database.
3.
Service Layer: The "Business Brain" where calculations and logic happen.
4.
Controller Layer: The "Traffic Cop" that receives user clicks and returns the HTML page.
5.
DTO Layer: Plain Java objects used to pass data safely between layers.
4. Annotation Reference Guide
Annotations are like "Labels" that tell Spring Boot to give a class or field special powers.
Component Annotations
•
@SpringBootApplication: The starting point of the whole app.
•
@Controller: Tells Spring this class handles web requests (URLs).
•
@Service: Tells Spring this class contains business logic.
•
@Repository: Tells Spring this class handles database operations.
•
@Configuration: Used for setup classes (like Security).
Database (JPA) Annotations
•
@Entity: Links a Java class to a Database table.
•
@Id: Marks the Primary Key.
•
@GeneratedValue: Tells MySQL to auto-increment the ID (1, 2, 3...).
•
@ManyToOne: Creates a link (Foreign Key). Example: Many Boats belong to One Supplier.
•
@Enumerated(EnumType.STRING): Saves a list of choices (like "ADMIN", "USER") as text in the DB.
•
@Transactional: Very Important. It ensures that a task is completed 100% or not at all. If you are saving a Sale and the computer crashes halfway, @Transactional rolls back the partial data so your database doesn't get corrupted.
Lombok Annotations
•
@Getter / @Setter: Generates all Getters and Setters automatically.
•
@NoArgsConstructor: Creates an empty constructor (required by JPA).
•
@RequiredArgsConstructor: Used for Dependency Injection. It connects the Service to the Repository automatically.
Validation Annotations
•
@NotBlank: Ensures a text field is not empty.
•
@DecimalMin: Ensures a number (like price) is not zero or negative.
•
@Valid: Tells Spring to check all these rules before saving the form.
5. End-to-End Workflow (The Life of a Request)
Imagine a user adding a new Fish Category:
1.
User Action: User types "Freshwater" in the Category form and clicks "Save".
2.
Controller: The @PostMapping in FishController receives the data in a FishCategoryRequestDTO.
3.
Validation: Spring checks the @NotBlank annotation. If empty, it sends an error back.
4.
Service: The Controller calls FishService.saveCategory().
5.
Mapping: FishMapper converts the DTO into a FishCategory Entity.
6.
Repository: categoryRepository.save(entity) is called. Hibernate (under JPA) generates the INSERT INTO... SQL command.
7.
Database: MySQL stores the row.
8.
Response: The user is redirected back to the list with a "Success" message.
6. Security Logic (Step-by-Step)
We used Spring Security with RBAC (Role-Based Access Control):
•
Authenticated: Everyone must login first.
•
Roles:
◦
ADMIN: Can see everything (Core + Finance + Reports).
◦
BUYER: Can only see Suppliers and Fish Catalog.
◦
WAREHOUSE: Can only see Cold Storage and Transport.
•
UI Protection: We used sec:authorize in Thymeleaf to hide the "Financials" menu from the WAREHOUSE user so they can't even see the button.
Summary for your Viva/Exam:
"Viyabari is a Spring Boot 3 application using a feature-based layered architecture. It uses JPA for ORM, Flyway for database versioning, and Thymeleaf for server-side rendering. Business logic is encapsulated in the Service layer, while Spring Security provides role-based access control to different modules like Logistics, Sales, and Finance."
