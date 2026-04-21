# SSD — System Specification Document

**EduCore Platform**  
**Version:** v1.0  
**Status:** Approved  
**Last updated:** 2026  

---

# 1. Purpose of the System

EduCore — це веб-платформа для дистанційного навчання, яка забезпечує керування курсами, завданнями, навчальними матеріалами та оцінюванням студентів.

Система реалізована як набір мікросервісів і працює через REST API.

EduCore взаємодіє з наступними компонентами:
- **User Service** — керування користувачами та ролями
- **Course Service** — управління курсами та модулями
- **Assignment Service** — створення та здача завдань
- **Grading Service** — перевірка та оцінювання робіт
- **File Storage Service** — зберігання файлів
- **API Gateway** — єдина точка входу

---

# 2. System Boundaries

## Included in scope

- REST API для студентів, викладачів та адміністрації
- Управління користувачами та ролями
- Створення та керування курсами
- Завантаження та здача завдань
- Система оцінювання
- Зберігання файлів (матеріали, роботи)
- Redis кешування (сесії, тимчасові дані)

---

## Excluded from scope

- Платіжні системи (не входить у EduCore MVP)
- AI автоматичне оцінювання (future extension)
- Мобільний застосунок (optional future scope)

---

# 3. Functional Requirements

---

## FR-01 — User Management

### FR-01.01 — User Registration
Система дозволяє користувачу створити обліковий запис (Student або Teacher).

**Endpoint:** `POST /api/users/register`  
**Success:** `201 Created`  
**Errors:** `400 Bad Request`, `409 Conflict`

---

### FR-01.02 — Authentication (JWT Login)
Система перевіряє облікові дані та видає JWT токен для доступу до системи.

**Endpoint:** `POST /api/users/login`  
**Success:** `200 OK`  
**Errors:** `401 Unauthorized`

---

### FR-01.03 — Role Management
Система дозволяє призначати та перевіряти ролі користувачів (Student, Teacher, Admin).

---

## FR-02 — Course Management

### FR-02.01 — Create Course
Викладач може створювати новий курс у системі.

**Endpoint:** `POST /api/courses`  
**Success:** `201 Created`  
**Errors:** `403 Forbidden`

---

### FR-02.02 — Manage Course Modules
Викладач може створювати та редагувати модулі курсу.

---

### FR-02.03 — Assign Teacher
Адміністратор може призначати викладача до курсу.

---

## FR-03 — Learning Materials

### FR-03.01 — View Materials
Студент може переглядати навчальні матеріали курсу.

---

## FR-04 — Assignments

### FR-04.01 — Upload Assignment
Студент може завантажити виконане завдання.

**Endpoint:** `POST /api/assignments/submit`  
**Success:** `200 OK`  
**Errors:** `400 Bad Request`, `401 Unauthorized`

---

### FR-04.02 — Store Student Answers
Система зберігає відповіді студентів у базі даних.

---

## FR-05 — Grading System

### FR-05.01 — Review Submissions
Викладач може переглядати виконані роботи студентів.

---

### FR-05.02 — Assign Grades
Викладач може виставляти оцінки студентам.

**Endpoint:** `POST /api/grades/evaluate`  
**Success:** `200 OK`  
**Errors:** `403 Forbidden`

---

## FR-06 — File Management

### FR-06.01 — File Upload
Система дозволяє завантаження файлів (матеріали, завдання).
```
**Endpoint:** `POST /api/files/upload`  
**Success:** `201 Created`  
**Errors:** `400 Bad Request`
```
---

# 4. Non-Functional Requirements

| Category | Requirement | Metric |
|----------|-------------|--------|
| Performance | Throughput | ≥ 1000 requests/min |
| Performance | Response time | ≤ 300 ms (p95) |
| Availability | Uptime | ≥ 99.9% |
| Security | Authentication | JWT |
| Security | Password hashing | BCrypt |
| Security | Transport | HTTPS (TLS 1.2+) |
| Scalability | Architecture | Stateless microservices |
| Reliability | Failure tolerance | Auto-restart via Kubernetes |

---

> **NFR-01:** Performance — 1000 rpm, p95 ≤ 300 ms  
> **NFR-02:** Availability — 99.9% uptime SLA  
> **NFR-03:** Security — JWT + HTTPS required  

---

# 5. Constraints and Assumptions

- Система працює тільки через REST API (без GraphQL/WebSocket)
- JWT використовується для авторизації
- PostgreSQL використовується як основна БД
- Redis використовується для кешування та сесій
- Файли зберігаються у File Storage Service (S3 або MinIO)
- RBAC реалізується через ролі: Student / Teacher / Admin

---

# 6. Target Audience

| Audience | Needs | Documents |
|----------|-------|-----------|
| Backend Developers | API, бізнес-логіка, архітектура | SSD, SDD |
| QA Engineers | вимоги, сценарії тестування | Test Strategy, Traceability Matrix |
| DevOps Engineers | інфраструктура, деплой | ISD |
| Business / Client | функціональність системи | SSD (functional section) |

---

*Цей документ є Single Source of Truth для функціональних вимог системи EduCore.*  
*Усі зміни вимог спочатку вносяться сюди, після чого синхронізуються з SDD та тестовою документацією.*
