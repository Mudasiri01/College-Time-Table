# 🕒 Smart College Time Table & Substitution Management System

> A full-functional web-based scheduling and management system built for educational institutions with multi-shift operations, automated conflict detection, and dynamic lecture swapping.

---

## 🌟 About The Project

Traditional college timetable creation and daily management (especially in institutions with second shifts, e.g., 12:10 PM to 5:20 PM) involve heavy manual paperwork and often result in scheduling conflicts. When teachers take unexpected leaves, managing substitutes and swapping lectures creates confusion in the hallways.

This system digitizes the entire process, offering **automated conflict prevention**, **second-shift time slot management**, and **instant teacher substitution/swapping** to keep college operations smooth and transparent.

---

## 🚀 Key Features

* **⏱️ Shift & Slot Customization:** Tailored specifically for institutions with second shifts (configured from 12:10 PM to 5:20 PM with custom breaks).
* **🔒 Zero-Conflict Engine:** Automatically prevents double-booking of teachers, rooms, and student batches before saving to the database.
* **🔄 Dynamic Lecture Swapping:** Easily shift or swap lectures back and forth during daily emergencies without disrupting the entire week's structure.
* **👨‍🏫 Smart Teacher Substitution:** When a teacher is absent, the system filters and displays *only* the teachers who are completely free during that specific time slot.
* **👥 Multi-Role Dashboards:**
  * **Admin / HOD Portal:** Master control for rooms, teachers, subjects, schedule generation, and substitutions.
  * **Teacher Portal:** Personal daily/weekly schedule view and live alerts for upcoming lectures.
  * **Student Portal:** Section-wise class routine access.
* **📄 Instant Export & Print:** One-click PDF generation for notice boards and WhatsApp sharing.

---

## 🛠️ Tech Stack

* **Frontend:** HTML5, CSS3, JavaScript (Vanilla / ES6+), Tailwind CSS
* **Backend:** Node.js, Express.js
* **Database:** MongoDB, Mongoose ODM
* **Tools & Environment:** Git, VS Code, Postman

---

## 📂 System Architecture & Workflow

1. **Setup Phase:** Admin registers Rooms, Teachers, Subjects, and Second-Shift Time Slots in MongoDB.
2. **Scheduling Phase:** Admin assigns lectures via dashboard; the backend validation engine checks for room and teacher availability.
3. **Daily Operations / Emergency Handling:** If a teacher is absent, Admin selects the slot, triggers the substitution filter, picks an available teacher, and updates the database in real time.
4. **User View:** Teachers and students log in to their respective portals to view updated schedules instantly.

---

## 🖥️ Screenshots / UI Preview

*(Aap yahan apne project ke dashboards ki screenshots add kar sakte hain)*
* **Admin Dashboard:** `[Insert Screenshot]`
* **Teacher Daily Schedule:** `[Insert Screenshot]`
* **Substitution Panel:** `[Insert Screenshot]`

---

## ⚙️ Installation & Setup (Local Development)

Apne local machine par project run karne ke liye yeh steps follow karein:

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/your-username/college-timetable-system.git](https://github.com/your-username/college-timetable-system.git)# College-Time-Table
