# SDD — Software Design Document

**EduCore Platform**  
**Version:** v1.0  
**Status:** Approved  
**Last updated:** 2026  

> References: [SSD — System Specification](SSD.md)

---

# 1. General Architecture

EduCore реалізована як **мікросервісна система** для освітньої платформи з використанням REST API та API Gateway як єдиної точки входу.

Архітектурний стиль: **Microservices Architecture**

---

## High-Level Architecture
```
Client / API Gateway
        │
        ▼
┌─────────────────────┐
│   Controller Layer  │  ← HTTP request handling, input validation
├─────────────────────┤
│   Service Layer     │  ← Business logic, token management
├─────────────────────┤
│  Repository Layer   │  ← Data access abstraction
├─────────────────────┤
│ PostgreSQL │ Redis  │  ← Persistent storage + session cache
└─────────────────────┘
```

---

# 2. Core Components

| Component | Technology | Responsibility |
|-----------|-----------|----------------|
| API Gateway | Nginx | Routing, authentication, rate limiting |
| User Service | Spring Boot | Users, roles, authentication (JWT) |
| Course Service | Spring Boot | Courses, modules, teachers assignment |
| Assignment Service | Spring Boot | Tasks upload & submission |
| Grading Service | Spring Boot | Evaluation and grading logic |
| File Storage Service | Spring Boot | File uploads (materials, tasks) |
| Database | PostgreSQL | Persistent relational storage |
| Cache | Redis | Sessions, temporary data, performance boost |
| Auth | JWT | Stateless authentication |

---

# 3. API Interfaces

Усі сервіси доступні через HTTPS через API Gateway.

---

## User Service

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/users/register` | Реєстрація користувача |
| POST | `/api/users/login` | Авторизація (JWT) |
| GET | `/api/users/{id}` | Отримання профілю |

---

## Course Service

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/courses` | Створення курсу |
| GET | `/api/courses` | Список курсів |
| POST | `/api/courses/{id}/assign-teacher` | Призначення викладача |

---

## Assignment Service

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/assignments` | Створення завдання |
| POST | `/api/assignments/submit` | Надсилання відповіді |
| GET | `/api/assignments/{id}` | Перегляд завдання |

---

## Grading Service

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/grades/evaluate` | Оцінювання роботи |
| GET | `/api/grades/{studentId}` | Отримання оцінок |

---

## File Storage Service

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/files/upload` | Завантаження файлу |
| GET | `/api/files/{id}` | Отримання файлу |

---

# 4. Component Interaction

## Example: Submit Assignment Flow
```
Student Client → API Gateway → Assignment Service → Auth Service (JWT verify)
                                            │
                                            ├→ File Storage Service (upload file)
                                            │            └→ Store in object storage
                                            │
                                            ├→ AssignmentRepository → PostgreSQL
                                            │
                                            └→ Event: "AssignmentSubmitted"
                                                    │
                                         Response ←── Submission status
```

---

## Step-by-step flow:

1. Користувач надсилає файл завдання
2. API Gateway маршрутизує запит в Assignment Service
3. File Storage Service зберігає файл
4. Assignment Service записує метадані в PostgreSQL
5. (Опціонально) Grading Service запускається для перевірки

---

# 5. Database Design

## Users
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE,
  password_hash VARCHAR(255),
  role VARCHAR(50),
  created_at TIMESTAMP
);
```
## Courses
```sql
CREATE TABLE courses (
  id UUID PRIMARY KEY,
  title VARCHAR(255),
  description TEXT,
  teacher_id UUID
);
```
## Assignments
```sql
CREATE TABLE assignments (
  id UUID PRIMARY KEY,
  course_id UUID,
  title VARCHAR(255),
  file_url TEXT,
  deadline TIMESTAMP
);
```
## Grades
```sql
CREATE TABLE grades (
  id UUID PRIMARY KEY,
  student_id UUID,
  assignment_id UUID,
  score INT,
  comment TEXT
);
```
# 6. Security Design

Система EduCore реалізує багаторівневу модель безпеки для захисту користувачів, даних та сервісів.

---

## 🔐 Основні механізми безпеки

- **JWT (JSON Web Token)** використовується для авторизації користувачів
- **RBAC (Role-Based Access Control)**:
  - Student
  - Teacher
  - Admin
- **Password hashing:** BCrypt (захист паролів від компрометації)
- **HTTPS (TLS 1.2+)** — шифрування всього трафіку
- **API Gateway rate limiting** — захист від DDoS та brute-force атак
- **File upload security** — перевірка файлів на malware (optional extension)

---

## 🧠 Security Principles

- **Stateless authentication** — використання JWT без збереження сесій на сервері
- **Least privilege access** — користувачі мають тільки необхідні права доступу
- **Service isolation** — мікросервіси ізольовані один від одного
- **Input validation** — перевірка даних на рівні Gateway та сервісів

---

# 7. Cross-Service Communication

Система EduCore використовує синхронну та розширювану асинхронну комунікацію між мікросервісами.

---

## 🔗 Основні принципи

- REST API між усіма сервісами
- JSON як основний формат обміну даними
- Синхронна комунікація через HTTP
- Асинхронні події (future extension: Kafka / RabbitMQ)

---

## 📡 Приклади взаємодії сервісів

```text id="comm_edc_01"
Assignment Service → File Storage Service
Grading Service → Assignment Service
Course Service → User Service
User Service → Notification Service (future)
```                                
