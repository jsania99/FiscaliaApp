# Fiscalía App

**Fiscalía App** is an **offline desktop-style web application** designed to manage criminal court cases (“juicios”) organized by *penal blocks*. The application is intended to run locally on a single computer without internet access, ensuring sensitive legal information remains isolated from external networks.

The system provides authentication, user management, and structured case tracking. Administrators can create and manage users, while standard users can access and manage judicial records.

The project uses a **Python backend (FastAPI + SQLite)** and a **React frontend (Vite + TypeScript)**. It can also be packaged into a **portable Windows executable** using PyInstaller so the application can run on machines without requiring Python or Node.js installations.

---

# Technology Stack

## Backend

* **Language:** Python 3.10+
* **Framework:** FastAPI
* **Database:** SQLite
* **Authentication:** bcrypt password hashing
* **API server:** Uvicorn (ASGI server)
* **Packaging:** PyInstaller (for creating a standalone executable)

### Main backend technologies

* **FastAPI** – modern Python web framework used to build the REST API
* **SQLite** – lightweight embedded relational database used for local storage
* **bcrypt** – secure password hashing
* **Uvicorn** – ASGI server used to run the FastAPI application
* **PyInstaller** – packages the application into a portable Windows executable

The backend manages:

* authentication
* user management
* session management
* judicial case records
* audit logging

---

## Frontend

* **Language:** TypeScript
* **Framework:** React
* **Build Tool:** Vite
* **Styling:** Tailwind CSS

### Main frontend technologies

* **React** – component-based UI framework
* **Vite** – fast development server and build tool
* **TypeScript** – static typing for safer frontend development
* **TailwindCSS** – utility-first CSS framework

The frontend is implemented as a **Single Page Application (SPA)** and communicates with the backend through a local API.

---

## Database

The application uses **SQLite**, an embedded relational database that stores data in a single file.

Database file:

```
data/database.db
```

Tables created at startup:

```
users
sessions
juicios
```

SQLite was chosen because:

* it requires no server
* it runs entirely locally
* it is ideal for offline desktop applications
* backups are simple file copies

---

# System Architecture

The application runs completely locally on the user's machine.

```
User
  │
  ▼
Web Browser
  │
  ▼
React Frontend (Vite SPA)
  │
  ▼
FastAPI Backend (Python)
  │
  ▼
SQLite Database
```

When packaged with PyInstaller, the backend runs automatically and the browser opens the interface.

---

# Requirements

To run the project in development mode:

### Backend

* Python **3.10+**

### Frontend

* Node.js **18+**
* npm

---

# First Launch

When the application starts for the first time there are **no users** in the database.

The frontend will display an **initial setup screen** allowing the creation of the **administrator account**.

The administrator can:

* create additional users
* assign roles (admin or standard user)
* access the user management panel

Passwords are securely stored using **bcrypt hashing**.

---

# Running the Application (Development Mode)

## 1. Start the Backend (API + Database)

From the project root directory (`FiscaliaApp`):

```
pip install -r requirements.txt
uvicorn backend.main:app --reload --host 127.0.0.1 --port 8000
```

The backend will run at:

```
http://localhost:8000
```

Interactive API documentation (Swagger UI):

```
http://localhost:8000/docs
```

The SQLite database will be automatically created at:

```
data/database.db
```

---

## 2. Start the Frontend (User Interface)

Open another terminal and run:

```
cd frontend
npm install
npm run dev
```

The frontend will run at:

```
http://localhost:5173
```

All requests to `/api` are proxied to the backend running on port **8000**.

### Summary

* Backend runs on **port 8000**
* Frontend runs on **port 5173**
* Access the application at:

```
http://localhost:5173
```

---

# Running the App From the Backend Only (Production Mode)

To serve the frontend and backend from a **single port**:

### Build the frontend

```
cd frontend
npm run build
```

### Start the backend

```
uvicorn backend.main:app --host 127.0.0.1 --port 8000
```

The application will be accessible at:

```
http://localhost:8000
```

The API will remain available at:

```
/api
```

---

# Portable Windows Executable (PyInstaller)

The application can be packaged into a **single Windows executable (.exe)** using PyInstaller.

This allows the app to run on a machine **without installing Python or Node.js**.

The executable automatically:

* starts the backend
* creates the database if it does not exist
* opens the browser
* runs entirely locally

---

# Quick Build Summary

From the project root directory:

```
cd frontend
npm install
npm run build
cd ..

pip install -r requirements.txt

pyinstaller FiscaliaApp.spec
```

The executable will be generated at:

```
dist/FiscaliaApp.exe
```

---

# Step-by-Step Executable Build

### Requirements

Machine used to build the executable must have:

* Python 3.10+
* Node.js 18+
* npm

---

### 1. Build the frontend

```
cd frontend
npm install
npm run build
cd ..
```

---

### 2. Install Python dependencies

```
pip install -r requirements.txt
```

This includes **PyInstaller**.

---

### 3. Generate the executable

From the project root directory:

```
pyinstaller FiscaliaApp.spec
```

The executable will be generated at:

```
dist/FiscaliaApp.exe
```

You can copy this file to any Windows computer.

---

# Executable Behavior

The executable is designed to provide a **simple double-click experience**.

### No console window

The executable is built with:

```
console=False
```

(PyInstaller windowed mode)

This means **no terminal window appears** when running the application.

---

### Automatic browser launch

After the application starts, the system automatically opens the default browser at:

```
http://localhost:8000
```

The user only needs to:

```
double click → FiscaliaApp.exe
```

---

### Closing the application

To stop the application you must terminate the process via:

* Windows Task Manager

A system tray icon could be added in future versions.

---

# Portable Usage

The application is designed to be **fully portable**.

You can:

* copy `FiscaliaApp.exe` anywhere
* place it on a USB drive
* run it from any folder

When executed, the application automatically creates:

```
data/
logs/
```

in the same directory as the executable.

---

# Project Structure

```
backend/
    FastAPI API
    SQLite integration
    authentication
    user management
    case management

frontend/
    React SPA
    Vite + TypeScript
    TailwindCSS
    UI for login, setup, cases and users

data/
    SQLite database (database.db)

logs/
    application audit logs
```

Additional documentation is available in:

```
backend/README.md
frontend/README.md
```

---

# Logging System

All **create, update, and delete operations** related to judicial records are logged.

Log file location:

```
logs/app.log
```

The log folder is automatically created when the first log entry is written.

Each log entry includes:

* user performing the action
* timestamp
* penal block
* operation type
* affected data

---

## Logged operations

### Creation

Logs all values of the newly created case.

### Update

Logs:

* case number
* modified field
* previous value
* new value

### Deletion

Logs the full data of the removed case.

---

## Example log entries

```
2026-03-17 10:30:00 | usuario: juan | penal: P6 | [Nueva entrada]: 21313,17/03/2026,V-11,344,Pendiente
2026-03-17 10:31:00 | usuario: juan | penal: P6 | [Edición]: nºjo: 324242 campo cambiado: fecha, valor nuevo: 18/03/2026, valor viejo: 10/03/2026
2026-03-17 10:32:00 | usuario: juan | penal: P6 | [Eliminación]: eliminada entrada con siguientes valores: 324242,10/03/2026,LL-1,22,Suspendido
```

These logs provide a basic **audit trail** for changes made within the system.

---

# Security Notes

Because the application is designed for **sensitive legal data**, it follows several principles:

* fully offline operation
* no external services
* local database storage
* hashed passwords (bcrypt)
* role-based access control
* audit logging of record modifications

This architecture ensures that all data remains **locally controlled and isolated from external networks**.

---

# License

This project is currently private and intended for internal use.
