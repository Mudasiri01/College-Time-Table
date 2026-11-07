# College Timetable Management System

A web-based system for creating, managing, and viewing college timetables. It detects scheduling conflicts automatically and lets teachers request lecture swaps with admin approval.

**Tech stack:** HTML, CSS, JavaScript (frontend) · Node.js + Express (backend) · MongoDB + Mongoose (database)

---

## Table of Contents

1. [Features](#features)
2. [Tech Stack](#tech-stack)
3. [Project Structure](#project-structure)
4. [Getting Started](#getting-started)
5. [Environment Variables](#environment-variables)
6. [Database Collections](#database-collections)
7. [Conflict Detection](#conflict-detection)
8. [Lecture Swap Feature](#lecture-swap-feature)
9. [API Endpoints](#api-endpoints)
10. [User Roles](#user-roles)
11. [Roadmap](#roadmap)
12. [Screenshots](#screenshots)
13. [Contributing](#contributing)
14. [License](#license)

---

## Features

### Core
- Secure login with JWT and bcrypt password hashing
- Role-based access: **Admin**, **Teacher**, **Student**
- Manage departments, programs, semesters, sections, courses, teachers, rooms, and time slots
- Create timetable entries: Course + Teacher + Room + Section + Day + Slot
- Weekly grid view (Mon-Sat × time slots)
- Edit, delete, and copy a timetable from a previous semester

### Conflict Detection
- Teacher clash (same teacher in two places)
- Room clash (same room booked twice)
- Section clash (same students in two classes)
- Room capacity check
- Lab courses allowed only in lab rooms
- Teacher maximum daily/weekly load
- Teacher unavailability slots

### Lecture Swap
- Teacher-to-teacher slot swap requests
- Substitution when a teacher is absent
- One-time (date-specific) or permanent swaps
- Peer acceptance, then admin approval
- Automatic validation before and at approval time
- Full swap history log

### Extras
- Free room finder and free teacher finder
- Notifications (in-app and email)
- Export to PDF and Excel
- Print-friendly view
- Color-coded classes (theory / lab)
- Filter by teacher, room, section, or department
- Responsive design and dark mode

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML5, CSS3, Vanilla JavaScript (Fetch API) |
| Backend | Node.js, Express.js |
| Database | MongoDB, Mongoose |
| Auth | JSON Web Tokens (JWT), bcrypt |
| Validation | express-validator |
| Charts | Chart.js |
| Export | pdfkit / jsPDF, exceljs |
| Email | Nodemailer |

---

## Project Structure

```
timetable-system/
├── config/
│   └── db.js
├── controllers/
│   ├── authController.js
│   ├── teacherController.js
│   ├── courseController.js
│   ├── roomController.js
│   ├── sectionController.js
│   ├── timetableController.js
│   └── swapController.js
├── middleware/
│   ├── auth.js              # verify JWT
│   ├── role.js              # role-based access
│   └── errorHandler.js
├── models/
│   ├── User.js
│   ├── Department.js
│   ├── Teacher.js
│   ├── Course.js
│   ├── Section.js
│   ├── Room.js
│   ├── TimeSlot.js
│   ├── TimetableEntry.js
│   ├── SwapRequest.js
│   └── Notification.js
├── routes/
│   ├── auth.js
│   ├── teachers.js
│   ├── courses.js
│   ├── rooms.js
│   ├── sections.js
│   ├── timetable.js
│   └── swaps.js
├── utils/
│   ├── conflictChecker.js
│   ├── exportPdf.js
│   └── exportExcel.js
├── public/
│   ├── css/
│   ├── js/
│   ├── admin/
│   ├── teacher/
│   ├── student/
│   └── index.html
├── .env
├── .gitignore
├── package.json
├── server.js
└── README.md
```

---

## Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) v18 or later
- [MongoDB](https://www.mongodb.com/) (local install or MongoDB Atlas)
- npm

> **Note:** MongoDB transactions (used for safe lecture swaps) require a **replica set**. MongoDB Atlas provides this by default. For local development, start `mongod` with `--replSet rs0` and run `rs.initiate()` once.

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/timetable-system.git
cd timetable-system

# 2. Install dependencies
npm install

# 3. Create the environment file
cp .env.example .env
# then edit .env with your values

# 4. (Optional) Seed the database with sample data
npm run seed

# 5. Start the server
npm run dev      # development (nodemon)
npm start        # production
```

Open **http://localhost:5000** in your browser.

### Default Admin Account (after seeding)

| Email | Password |
|---|---|
| admin@college.edu | Admin@123 |

Change this password immediately after first login.

---

## Environment Variables

Create a `.env` file in the project root:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/timetable
JWT_SECRET=replace_with_a_long_random_string
JWT_EXPIRES_IN=7d

# Email notifications (optional)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
```

Never commit your `.env` file.

---

## Database Collections

| Collection | Key Fields |
|---|---|
| users | name, email, passwordHash, role |
| departments | name, code |
| teachers | userId, departmentId, subjects[], availability[], maxLoad |
| courses | code, name, creditHours, type, departmentId |
| sections | name, program, semester, strength |
| rooms | name, capacity, type (class/lab), building |
| timeslots | label, startTime, endTime, order |
| timetableentries | courseId, teacherId, roomId, sectionId, day, slotId, semester |
| swaprequests | type, entryA, entryB, date, isPermanent, requestedBy, status, reason |
| notifications | userId, message, read, createdAt |

---

## Conflict Detection

Every time a class is created, edited, or swapped, the system checks:

1. **Teacher clash:** the teacher is not already teaching at that day and slot
2. **Room clash:** the room is free at that day and slot
3. **Section clash:** the section has no other class at that day and slot
4. **Capacity:** room capacity is at least the section strength
5. **Room type:** lab courses are placed only in lab rooms
6. **Teacher load:** daily and weekly limits are not exceeded
7. **Availability:** the slot is not marked unavailable by the teacher

When updating or swapping, the entries being changed are excluded from the check so they do not conflict with themselves.

---

## Lecture Swap Feature

### Swap types
| Type | Description |
|---|---|
| Slot swap | Two classes exchange day and time slot |
| Teacher swap | Two teachers exchange their lectures |
| Substitution | One teacher covers another's class |
| Room swap | Two classes exchange rooms |

### Workflow

```
Teacher requests → System validates → Peer teacher accepts
        → Admin approves → Timetable updated → Notifications sent
```

**Statuses:** `pending` → `peer_accepted` → `approved`, or `rejected` / `cancelled`

### One-time vs permanent
- **One-time:** stored as a date-specific override. The base timetable is unchanged.
- **Permanent:** updates the base timetable for the rest of the semester.

### Edge cases handled
- Requests expire automatically if not answered within 24 hours
- Conflicts are re-validated at approval time
- Lab sessions spanning consecutive slots are swapped as a block
- An entry with a pending request cannot be used in a second request
- Swaps use database transactions so a swap never completes halfway

---

## API Endpoints

### Auth
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/login` | Login and receive a JWT |
| POST | `/api/auth/register` | Create a user (admin only) |
| GET | `/api/auth/me` | Current user profile |

### Master Data (admin)
| Method | Endpoint | Description |
|---|---|---|
| GET/POST | `/api/teachers` | List / create teachers |
| PUT/DELETE | `/api/teachers/:id` | Update / delete a teacher |
| GET/POST | `/api/courses` | List / create courses |
| GET/POST | `/api/rooms` | List / create rooms |
| GET/POST | `/api/sections` | List / create sections |
| GET/POST | `/api/timeslots` | List / create time slots |

### Timetable
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/timetable?section=&teacher=&room=` | Filtered timetable |
| POST | `/api/timetable` | Create an entry (runs conflict check) |
| PUT | `/api/timetable/:id` | Update an entry |
| DELETE | `/api/timetable/:id` | Delete an entry |
| GET | `/api/timetable/free-rooms?day=&slot=` | Find free rooms |
| GET | `/api/timetable/free-teachers?day=&slot=` | Find free teachers |
| GET | `/api/timetable/export/pdf` | Export as PDF |
| GET | `/api/timetable/export/excel` | Export as Excel |

### Swaps
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/swaps` | Create a swap request |
| GET | `/api/swaps/my` | Sent and received requests |
| GET | `/api/swaps/suggestions?entryId=` | Valid swap options |
| PATCH | `/api/swaps/:id/accept` | Peer teacher accepts |
| PATCH | `/api/swaps/:id/approve` | Admin approves |
| PATCH | `/api/swaps/:id/reject` | Reject a request |
| PATCH | `/api/swaps/:id/cancel` | Requester cancels |

All routes except login require the header `Authorization: Bearer <token>`.

---

## User Roles

| Role | Can do |
|---|---|
| **Admin** | Manage all master data, build timetables, approve swaps, view reports |
| **Teacher** | View own timetable, set availability, request and respond to swaps |
| **Student** | View section timetable, search by teacher, room, or course |

---

## Roadmap

- [x] Authentication and roles
- [x] Master data CRUD
- [x] Timetable creation with conflict detection
- [x] Weekly grid view
- [ ] Dashboards and charts
- [ ] PDF / Excel export
- [ ] Lecture swap with approval workflow
- [ ] Notifications (in-app and email)
- [ ] Drag-and-drop timetable editing
- [ ] Auto-generate timetable (backtracking algorithm)
- [ ] Exam datesheet module

---

## Screenshots

Add screenshots here after building the UI.

```
/screenshots/login.png
/screenshots/admin-dashboard.png
/screenshots/timetable-grid.png
/screenshots/swap-request.png
```

---

## Contributing

1. Fork the repository
2. Create a branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## License

This project is licensed under the MIT License.

---

## Author

**Your Name**
Email: your_email@example.com
GitHub: [@your-username](https://github.com/your-username)
