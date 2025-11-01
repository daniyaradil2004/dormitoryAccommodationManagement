# Dormitory Accommodation Management

## Overview
This repository contains a complete solution for the **Object-Oriented Thinking** assignment: design of a class hierarchy for a Dormitory Accommodation Management system.  

### Included sections:
- Functional and non-functional requirements  
- Use cases (with actors and basic scenarios)  
- Identified objects, classes and relationships  
- CRC cards (selected)  
- Class diagrams (textual + PlantUML)  

---

## 1. Requirements

### 1.1 Functional Requirements
1. **Student registration & profile management** — register new students, maintain personal details, contact info, study program, and emergency contacts.  
2. **Room inventory & allocation** — define rooms (building, floor, room number), capacity (single/ double/ multi), room features (AC, bathroom, accessible), and allocate students to rooms.  
3. **Booking & waiting list** — accept booking requests, support temporary holds, maintain waiting lists for full room types.  
4. **Contracts & occupancy periods** — create, renew and terminate accommodation contracts with start/end dates, fees and special conditions.  
5. **Billing & payments** — calculate monthly/term fees, generate invoices, accept payment records and track outstanding balances.  
6. **Maintenance requests** — students can submit maintenance requests; staff can update status, assign technicians and close requests.  
7. **Visitor logging & access control** — register short-term visitors, track entry/exit times, and enforce visitor policies.  
8. **Search & reporting** — search students, rooms, and generate reports: occupancy rates, overdue payments, maintenance backlog, check-in/out lists.  
9. **Notifications** — send notifications (email/SMS placeholders) for room assignment, contract expiration, payment reminders and maintenance updates.  
10. **Admin & staff roles** — role-based actions: administrators, housing officers, maintenance staff.  

### 1.2 Non-functional Requirements
1. **Usability** — intuitive UI flows (not implemented here), clear error messages and quick lookup for common tasks.  
2. **Scalability** — support hundreds to thousands of students and rooms; design with separation of concerns to allow DB scaling.  
3. **Reliability** — persistent storage for critical data (contracts, payments); atomic updates for room allocation.  
4. **Security & privacy** — role-based access control (RBAC) to limit sensitive data; encryption for personally identifiable information (PII) in storage and transit.  
5. **Maintainability** — modular OOP design, clear class responsibilities and documentation to simplify future extension.  
6. **Extensibility** — support for internationalization, additional billing rules, and integration with campus systems.  
7. **Performance** — queries for allocations and reports should run in acceptable time (indexable fields: studentId, roomId, status).  

---

## 2. Use Cases

### Actors
- **Student** — requests accommodation, submits maintenance requests, views their profile and invoice.  
- **HousingOfficer** — manages rooms, allocations, contracts, and reporting.  
- **MaintenanceStaff** — handles maintenance requests.  
- **FinanceOfficer** — manages invoices, payments, and generates financial reports.  
- **Visitor (external)**  

### Use Cases (brief)
1. **Register Student (Student, HousingOfficer)** — Student provides personal data; HousingOfficer verifies and approves.  
2. **Search Available Rooms (Student, HousingOfficer)** — Query by date, capacity, building, features.  
3. **Book Room / Place on Waiting List (Student, HousingOfficer)** — If available, create temporary hold; else add to waiting list.  
4. **Create Contract (HousingOfficer)** — Link student and room, set start/end dates, fees, and terms.  
5. **Check In / Check Out (HousingOfficer)** — Update occupancy status and room availability.  
6. **Submit Maintenance Request (Student)** — Student raises request; MaintenanceStaff assigned and status tracked.  
7. **Generate Invoice (FinanceOfficer)** — Produce invoice for contract period and track payment status.  
8. **Assign Visitor (Student, HousingOfficer)** — Register visitor and set allowed times.  
9. **Generate Reports (HousingOfficer, FinanceOfficer)** — Occupancy, payments overdue, maintenance backlog.  

---

## 3. Objects, Classes & Relationships

### Key Domain Classes
- **Student** — stores student profile and ID.  
- **Room** — represents a physical room; attributes include capacity and features.  
- **Building** — groups rooms by building and floor.  
- **Contract** — links a Student and a Room over a time period with financial terms.  
- **BookingRequest / WaitingListEntry** — requests for rooms.  
- **Invoice / Payment** — billing objects.  
- **MaintenanceRequest** — maintenance ticket with statuses.  
- **User / Staff / HousingOfficer / FinanceOfficer** — system users with roles.  
- **VisitorLog** — records visits.  

### Relationships (high-level)
- `Building` 1..* → `Room` (composition)  
- `Room` 0..* → `Contract` (a room can have many historical contracts but at most one active overlapping)  
- `Student` 0..* → `Contract` (a student can sign multiple contracts over time)  
- `Contract` 1 → `Invoice` (one contract produces invoices over time)  
- `Invoice` 0..* → `Payment` (partial or multiple payments)  
- `Room` 0..* → `MaintenanceRequest`  
- `Student` 0..* → `MaintenanceRequest` (raised by student)  

---

## 4. CRC Cards (selected)

### Student  
- **Responsibilities:** hold identity, contact info, enrollments; request bookings; submit maintenance.  
- **Collaborators:** Contract, BookingRequest, MaintenanceRequest, Invoice  

### Room  
- **Responsibilities:** keep capacity, features, occupancy status; accept/release allocations.  
- **Collaborators:** Building, Contract, MaintenanceRequest  

### Contract  
- **Responsibilities:** bind Student and Room for dates; calculate fees; produce invoices.  
- **Collaborators:** Student, Room, Invoice  

### MaintenanceRequest  
- **Responsibilities:** log issue; track status; assign technician; close request.  
- **Collaborators:** Student, MaintenanceStaff, Room  

---

## 5. Class Diagram

### 5.1 PlantUML (copy to a PlantUML viewer)

```plantuml
@startuml
class Building {
  - id: String
  - name: String
  - address: String
  - floors: int
}
class Room {
  - id: String
  - buildingId: String
  - floor: int
  - roomNumber: String
  - capacity: int
  - features: List<String>
  - status: RoomStatus
}
class Student {
  - id: String
  - firstName: String
  - lastName: String
  - email: String
  - phone: String
  - studyProgram: String
}
class Contract {
  - id: String
  - studentId: String
  - roomId: String
  - startDate: Date
  - endDate: Date
  - monthlyFee: double
  - status: ContractStatus
}
class Invoice {
  - id: String
  - contractId: String
  - issueDate: Date
  - dueDate: Date
  - amount: double
  - status: InvoiceStatus
}
class Payment {
  - id: String
  - invoiceId: String
  - paidDate: Date
  - amount: double
  - method: String
}
class MaintenanceRequest {
  - id: String
  - roomId: String
  - raisedByStudentId: String
  - description: String
  - status: MaintenanceStatus
  - createdAt: Date
}
class User {
  - userId: String
  - username: String
  - passwordHash: String
  - roles: List<String>
}

Building "1" *-- "*" Room
Room "1" o-- "*" Contract
Student "1" o-- "*" Contract
Contract "1" -- "*" Invoice
Invoice "1" -- "*" Payment
Room "1" -- "*" MaintenanceRequest
Student "1" -- "*" MaintenanceRequest
User <|-- HousingOfficer
User <|-- FinanceOfficer
User <|-- MaintenanceStaff
@enduml
