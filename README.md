# OceanView Hotel Management System

A full-featured hotel management web application built with Java Servlets, JSP, and MySQL. Designed for hotel staff and administrators to manage reservations, rooms, billing, payments, and operations from a single dashboard.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Setup](#database-setup)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [Default Login Credentials](#default-login-credentials)
- [Module Overview](#module-overview)
- [Role-Based Access](#role-based-access)
- [Screenshots](#screenshots)

---

## Features

- **Authentication** — Secure login/logout with session management
- **Role-Based Access** — Admin and Staff roles with separate permissions
- **Reservations** — Full CRUD: create, view, edit, cancel reservations with status tracking
- **Room Management** — Manage room inventory with types, pricing, and availability
- **Check-In / Check-Out** — Process guest arrivals and departures with extra charges
- **Billing** — Generate invoices, folios, and a revenue dashboard
- **Payments** — Multi-method payments (Cash, Card, Bank Transfer) per reservation
- **Reports** — Staff performance, room occupancy, and payment reports with PDF/Excel export
- **Banks** — Manage bank list used in payment dropdowns
- **User Management** — Admin can create/edit/deactivate system users
- **Audit Logs** — Full action trail with filtering by user, action, and date
- **System Settings** — Configure hotel name, currency, address, phone, and tax rate from the UI

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Java 21 |
| Runtime | Apache Tomcat 11.0 |
| Servlet API | Jakarta EE 5.0 |
| Database | MySQL 8.x |
| JDBC Driver | mysql-connector-j 9.6.0 |
| Frontend | Bootstrap 5.3.3, HTML5, Vanilla JS |
| IDE | Eclipse IDE with WTP (Web Tools Platform) |

---

## Project Structure

```
oceanview/
├── src/
│   └── main/
│       ├── java/oceanview/
│       │   ├── Audit/              # AuditLogger utility
│       │   ├── database/           # DBConnection (JDBC)
│       │   ├── filter/             # AuthFilter (session guard)
│       │   ├── listener/           # SettingsListener (app startup)
│       │   ├── model/              # Entity classes (User, Reservation, Room, ...)
│       │   ├── dao/                # Data Access Objects (SQL queries)
│       │   ├── service/            # Business logic layer
│       │   └── servlet/            # HTTP request handlers
│       └── webapp/
│           ├── index.jsp           # Redirects to /login
│           └── WEB-INF/
│               ├── web.xml         # Servlet, filter & listener config
│               ├── lib/            # mysql-connector-j JAR
│               ├── sql/            # Database setup scripts
│               └── views/          # JSP views organized by feature
│                   ├── login.jsp
│                   ├── admin-dashboard.jsp
│                   ├── staff-dashboard.jsp
│                   ├── reservations/
│                   ├── rooms/
│                   ├── checkin/
│                   ├── checkout/
│                   ├── billing/
│                   ├── banks/
│                   ├── users/
│                   ├── audit/
│                   ├── reports/
│                   └── settings/
└── build/
    └── classes/                    # Compiled bytecode (git-ignored)
```

---

## Database Setup

### 1. Create the database

```sql
CREATE DATABASE oceanview_db;
USE oceanview_db;
```

### 2. Run the setup scripts in order

All scripts are located in `src/main/webapp/WEB-INF/sql/`:

```
1. users_setup.sql          -- users table + seed accounts
2. rooms_setup.sql          -- rooms table + sample rooms
3. reservations_setup.sql   -- reservations table
4. payment_setup.sql        -- payments table
5. extra_charges_setup.sql  -- extra charges table
6. audit_log_setup.sql      -- audit_log table
7. system_settings_setup.sql -- system_settings table + defaults
```

### 3. Database schema overview

| Table | Description |
|-------|-------------|
| `users` | System accounts with roles (ADMIN / STAFF) |
| `reservations` | Guest bookings with status lifecycle |
| `rooms` | Hotel room inventory |
| `payments` | Payment transactions per reservation |
| `extra_charges` | Add-on charges during stay |
| `banks` | Bank catalog for payment options |
| `audit_log` | Tracks all system actions |
| `system_settings` | Key-value store for app configuration |

---

## Configuration

### Database credentials

Edit `src/main/java/oceanview/database/DBConnection.java`:

```java
private static final String URL  = "jdbc:mysql://localhost:3306/oceanview_db";
private static final String USER = "root";
private static final String PASS = "your_password";
```

> Update `USER` and `PASS` to match your local MySQL credentials.

### System settings

After the app starts, log in as admin and go to **Settings** (`/settings`) to configure:

| Setting | Default Value |
|---------|--------------|
| Hotel Name | OceanView Hotel |
| Hotel Address | 123 Coastal Avenue, Seaside City |
| Hotel Phone | +94 11 234 5678 |
| Currency | LKR |
| Tax Rate | 0% |

These are stored in the `system_settings` table and loaded at startup by `SettingsListener`.

---

## Running the Application

### Prerequisites

- Java 21 JDK
- Apache Tomcat 11.0
- MySQL 8.x
- Eclipse IDE with WTP (or any servlet container)

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/ishira-web/ovr_reservation_system.git
   ```

2. **Import into Eclipse**
   - File → Import → Existing Projects into Workspace
   - Select the cloned folder

3. **Set up the database**
   - Create `oceanview_db` in MySQL
   - Run all SQL scripts from `src/main/webapp/WEB-INF/sql/`

4. **Update DB credentials**
   - Edit `DBConnection.java` with your MySQL username and password

5. **Add Tomcat to Eclipse**
   - Window → Preferences → Server → Runtime Environments → Add → Apache Tomcat 11.0

6. **Deploy and run**
   - Right-click project → Run As → Run on Server
   - Access at: `http://localhost:8080/oceanview`

---

## Default Login Credentials

| Role | Username | Password |
|------|----------|----------|
| Admin | `admin` | `admin123` |
| Staff | `staff1` | `staff123` |

> Passwords are hashed using SHA-256 in the database.

---

## Module Overview

| URL | Module | Description |
|-----|--------|-------------|
| `/login` | Login | Authenticate users |
| `/dashboard` | Dashboard | Role-aware home screen |
| `/reservations` | Reservations | Create, view, edit, cancel bookings |
| `/rooms` | Rooms | Manage room types, pricing, status |
| `/checkin` | Check-In | Mark reservation as checked in |
| `/checkout` | Check-Out | Process departure, finalize bill |
| `/billing` | Billing | Invoices, folios, revenue dashboard |
| `/reports` | Reports | Staff/room/payment reports, export |
| `/banks` | Banks | Manage banks for payment methods |
| `/users` | Users | User account management |
| `/audit` | Audit Logs | View system action history |
| `/settings` | Settings | Configure hotel and app settings |

---

## Role-Based Access

| Feature | Admin | Staff |
|---------|-------|-------|
| View Dashboard | ✅ | ✅ |
| Manage Reservations | ✅ | ✅ |
| Check-In / Check-Out | ✅ | ✅ |
| View Billing | ✅ | ✅ |
| Manage Rooms | ✅ | ❌ |
| Manage Banks | ✅ | ❌ |
| Manage Users | ✅ | ❌ |
| View Audit Logs | ✅ | ❌ |
| System Settings | ✅ | ❌ |
| View Reports | ✅ | ✅ |

---

## Architecture

The application follows a strict **3-tier MVC architecture**:

```
Request → AuthFilter → Servlet → Service → DAO → MySQL
                          ↓
                         JSP (View)
```

- **Filter** — `AuthFilter` protects all routes, redirects unauthenticated users to `/login`
- **Servlet** — Handles HTTP requests, validates roles, calls services, forwards to JSP
- **Service** — Contains business logic and validation rules
- **DAO** — Executes SQL using `PreparedStatement`, maps `ResultSet` to model objects
- **JSP** — Renders HTML using data set as request attributes by the servlet

---

## License

This project is developed for academic and educational purposes.
