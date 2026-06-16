# 📝 Online Examination Application

A full-stack web application that enables teachers to create and manage online exams, and students to take them — complete with automatic grading, attempt tracking, and result review.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
  - [Running with Docker](#running-with-docker)
- [Configuration](#configuration)
- [API Reference](#api-reference)
- [Roles & Permissions](#roles--permissions)
- [Running Tests](#running-tests)
- [Contributing](#contributing)

---

## Overview

The Online Examination Application is a two-role platform built for academic use. Teachers can create multiple-choice exams with configurable passcodes, time limits, and attempt caps. Students discover and join exams, answer questions within the allotted time, and immediately see their scored results. Both roles get dedicated dashboards that surface the information most relevant to them.

---

## Features

### Teacher
- Create exams with multiple-choice questions and configurable options
- Set exam start/end windows, duration, and maximum attempt count
- Generate or update passcodes to control access
- View all exam results submitted by students
- Dashboard with an overview of all created exams

### Student
- Register and log in with a role-based account
- Join exams using an exam code and optional passcode
- Take timed tests through a dedicated test-taking interface
- View detailed results and correct answers after submission
- Track exam attempt history from the dashboard

### General
- Session-based authentication with role enforcement
- Auto-graded multiple-choice answers on submission
- Swagger UI for interactive API exploration (`/swagger-ui.html`)
- Dockerised backend for straightforward deployment

---

## Tech Stack

### Backend
| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 4.0.5 |
| Security | Spring Security |
| Persistence | Spring Data JPA + Hibernate |
| Database | PostgreSQL (Supabase) |
| API Docs | SpringDoc OpenAPI (Swagger UI) |
| Build Tool | Maven (Maven Wrapper included) |
| Utilities | Lombok |

### Frontend
| Layer | Technology |
|---|---|
| Language | JavaScript (ES Modules) |
| Framework | React 19 |
| Routing | React Router DOM 7 |
| UI Library | Material UI (MUI) 9 + Lucide React |
| Styling | Tailwind CSS 3 |
| HTTP Client | Axios |
| Build Tool | Vite 8 |

---

## Project Structure

```
online-examination/
├── app/                        # Spring Boot backend
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/hcmut/online_examination/
│   │   │   │   ├── config/         # Security & CORS configuration
│   │   │   │   ├── controller/     # REST controllers (Auth, Exam)
│   │   │   │   ├── dto/            # Request / Response data transfer objects
│   │   │   │   ├── entity/         # JPA entities (User, Exam, Question, ...)
│   │   │   │   ├── exception/      # Custom exception classes
│   │   │   │   ├── handler/        # Global exception handlers
│   │   │   │   ├── mappers/        # Entity ↔ DTO mapping
│   │   │   │   ├── repository/     # Spring Data JPA repositories
│   │   │   │   ├── service/        # Business logic (Auth, Exam)
│   │   │   │   └── AppApplication.java
│   │   │   └── resources/
│   │   │       └── application.properties
│   │   └── test/               # Unit & integration tests
│   ├── Dockerfile
│   └── pom.xml
│
└── frontend/
    └── app-ui/                 # React + Vite frontend
        ├── src/
        │   ├── components/     # Shared layout components (sidebars)
        │   ├── pages/
        │   │   ├── landingpage/
        │   │   ├── student/    # Dashboard, TakeTest, TestInterface, ResultDetail
        │   │   └── teacher/    # Dashboard, CreateTest, ExamResults
        │   ├── services/       # Axios API client & auth service
        │   └── App.jsx         # Route definitions
        ├── index.html
        └── package.json
```

---

## Prerequisites

- **Java 21** or later
- **Maven 3.9+** (or use the included `./mvnw` wrapper)
- **Node.js 18+** and **npm**
- **PostgreSQL** database (a Supabase connection is pre-configured; replace it with your own for production)
- **Docker** (optional, for containerised deployment)

---

## Getting Started

### Backend Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd online-examination/app
   ```

2. **Configure the database**

   Edit `src/main/resources/application.properties` with your own PostgreSQL credentials (see [Configuration](#configuration)).

3. **Build and run**
   ```bash
   # Using the Maven wrapper
   ./mvnw spring-boot:run

   # Or build a JAR first
   ./mvnw clean package -DskipTests
   java -jar target/app-0.0.1-SNAPSHOT.jar
   ```

   The backend starts on **`http://localhost:8080`** by default.

4. **Explore the API**

   Open **`http://localhost:8080/swagger-ui.html`** to browse and test all endpoints interactively.

---

### Frontend Setup

1. **Navigate to the frontend directory**
   ```bash
   cd online-examination/frontend/app-ui
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the development server**
   ```bash
   npm run dev
   ```

   The app is served at **`http://localhost:5173`**.

4. **Build for production**
   ```bash
   npm run build
   ```

---

### Running with Docker

A `Dockerfile` is provided for the backend. It uses a two-stage build to keep the final image small.

```bash
cd online-examination/app

# Build the image
docker build -t online-examination-backend .

# Run the container
docker run -p 8080:8080 online-examination-backend
```

> **Note:** Make sure your `application.properties` references a PostgreSQL instance accessible from inside the Docker container before building the image, or pass environment variables at runtime.

---

## Configuration

All backend configuration lives in `app/src/main/resources/application.properties`.

| Property | Description |
|---|---|
| `spring.datasource.url` | JDBC connection URL for PostgreSQL |
| `spring.datasource.username` | Database username |
| `spring.datasource.password` | Database password |
| `spring.jpa.hibernate.ddl-auto` | Schema strategy (`validate` in production; `update` for development) |
| `spring.jpa.show-sql` | Log SQL statements to the console |

The frontend Axios client points to `http://localhost:8080` by default. To target a different backend host, update the base URL in `frontend/app-ui/src/services/api.js`.

---

## API Reference

The full interactive API specification is available at `/swagger-ui.html` when the backend is running. A summary of the main endpoints is provided below.

### Authentication — `/api/auth`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/register` | Register a new Student or Teacher account |
| `POST` | `/api/auth/login` | Authenticate and retrieve session credentials |

**Register request body**
```json
{
  "username": "student123",
  "fullName": "John Doe",
  "password": "StrongPass1",
  "role": "STUDENT"
}
```

Password must be at least 8 characters and contain at least one uppercase letter, one lowercase letter, and one number.

---

### Exam — `/api/exam`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/exam/create` | Create a new exam (Teacher) |
| `GET` | `/api/exam/info/{examCode}` | Get exam metadata (no correct answers) |
| `GET` | `/api/exam/owner/{ownerId}` | List all exams owned by a teacher |
| `GET` | `/api/exam/teacher/list` | Overview list of exams created by a teacher |
| `POST` | `/api/exam/update-passcode` | Set or update the exam passcode |
| `POST` | `/api/exam/join` | Join an exam with code and optional passcode |
| `POST` | `/api/exam/submit` | Submit answers and receive a graded result |
| `POST` | `/api/exam/correct-answers` | Retrieve correct answers for an exam |
| `GET` | `/api/exam/attempt-count` | Count how many times a student attempted an exam |
| `GET` | `/api/exam/history` | Fetch a student's full exam history |
| `GET` | `/api/exam/results` | Get all results for an exam (Teacher) |
| `GET` | `/api/exam/results?examCode=&userId=` | Get results filtered by student (Teacher) |

---

## Roles & Permissions

| Action | Student | Teacher |
|---|---|---|
| Register / Login | ✅ | ✅ |
| Join an exam | ✅ | — |
| Take and submit an exam | ✅ | — |
| View own results & history | ✅ | — |
| Create an exam | — | ✅ |
| Update exam passcode | — | ✅ |
| View all student results | — | ✅ |
| Access correct answers | — | ✅ |

---

## Running Tests

```bash
cd online-examination/app

# Run all tests
./mvnw test
```

The test suite uses an **H2 in-memory database**, so no external database connection is required. Tests cover the authentication controller and basic application context loading.

---

## Contributing

1. Fork the repository and create a feature branch.
2. Follow the existing code conventions (Lombok, Spring patterns, Vite/React structure).
3. Write or update tests for any changed behaviour.
4. Submit a pull request with a clear description of the change.
