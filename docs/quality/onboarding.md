# Developer Onboarding Guide — EduCore Platform

**Version:** v1.0  
**Last updated:** 2026  

> Architecture reference: [SDD](../architecture/SDD.md) | Deployment: [ISD](../architecture/ISD.md)

---

# 1. Prerequisites

Перед початком роботи переконайтесь, що у вас встановлено:

- **Java 17+**
- **Maven або Gradle**
- **Node.js 18+**
- **Docker + Docker Compose**
- **Git**
- **PostgreSQL (optional, якщо не через Docker)**

---

## 2. Local Setup

### Step 1 — Clone the repository

```bash
git clone https://github.com/your-org/educore.git
cd educore
```

### Step 2 — Install dependencies

```bash
npm install
```

### Step 3 — Configure environment

```bash
cp .env.example .env
```

Edit `.env` with your local values:

```env
SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/educore_db
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=password

REDIS_HOST=localhost
REDIS_PORT=6379

JWT_SECRET=your-secret-key
SERVER_PORT=8080
```
### Step 4 — Start infrastructure

```bash
docker-compose up -d
```

### Step 5 — Run backend

```bash
mvn spring-boot:run
```

### Step 6 — Run frontend

```bash
cd frontend
npm run dev
```
Система буде доступна:

- Backend: http://localhost:8080
- Frontend: http://localhost:3000
---


## 3. Running Tests

**Backend (Spring Boot):**

```bash
mvn test              # всі тести
mvn -Dtest=*UnitTest test        # тільки unit-тести
mvn -Dtest=*IntegrationTest test # integration тести
mvn verify           # повний цикл (включно з integration)
```

```react
npm run test          # всі тести
npm run test:coverage # з покриттям
```
Деталі дивись https://chatgpt.com/quality/test-strategy.md

## 4. Git Flow

```
main ────────────────────────────────────── production
  └── develop ──────────────────────────── integration
         └── feature/FR-01-registration ── your work
         └── feature/FR-02.01-course-create ── your work
         └── feature/FR-04.01-assignment-submit ── your work
         └── fix/login-error ── your work
```

---

### Branch Strategy

| Branch | Purpose |
|--------|--------|
| `main` | Стабільна версія (production) |
| `develop` | Основна гілка розробки |
| `feature/*` | Реалізація нових функцій |
| `fix/*` | Виправлення багів |
| `hotfix/*` | Критичні виправлення в production |
| `docs/*` | Оновлення документації |

---

### Naming Convention

| Type | Pattern | Example |
|------|--------|--------|
| Feature | `feature/FR-XX-description` | `feature/FR-02-course-create` |
| Bug fix | `fix/short-description` | `fix/jwt-error` |
| Docs | `docs/update` | `docs/update-ssd` |
| Hotfix | `hotfix/critical-issue` | `hotfix/security-fix` |

---

### Workflow

1. Створити гілку від `develop`:
```bash
git checkout develop
git checkout -b feature/FR-04.01-assignment-submit
```
2. Виконати зміни та зробити commit:
```bash
git commit -m "feat(assignment): add submission feature (FR-04.01)"
```
3. Запушити гілку:
```bash
git push origin feature/FR-04.01-assignment-submit
```
4. Створити Pull Request → develop

### Pull Request rules

1. Гілка повинна бути актуальною відносно `develop`.
2. Усі тести повинні проходити (CI перевірка).
3. Мінімум 1 рев’ю від іншого розробника.
4. Опис PR повинен містити ID вимоги (наприклад: `Implements FR-04.01`).

---

## 5. Project Structure

```
auth-service/
├── src/
│   ├── controllers/     ← HTTP handlers (Express routes)
│   ├── services/        ← Business logic
│   ├── repositories/    ← Data access (Prisma)
│   ├── middleware/       ← Auth, rate limiting, error handling
│   ├── modules/
│   │   └── token/       ← JWT generation and verification
│   └── app.ts           ← Express app setup
├── tests/
│   ├── unit/
│   └── integration/
├── prisma/
│   └── schema.prisma    ← Database schema
├── docs/                ← Documentation (this repository)
├── keys/                ← JWT keys (git-ignored)
├── docker-compose.yml
└── package.json
```
---
## 6. Useful Commands

**Backend (Spring Boot):**

```bash
mvn clean install        # збірка проєкту
mvn spring-boot:run      # запуск backend
mvn test                 # запуск тестів
```
Frontend (React):

```bash
npm install              # встановлення залежностей
npm run dev              # запуск dev-сервера
npm run build            # production build
npm run lint             # перевірка коду (ESLint)
```
Docker / Infrastructure:

```bash
docker-compose up -d     # запуск контейнерів (PostgreSQL, Redis)
docker-compose down      # зупинка контейнерів
docker-compose logs -f   # перегляд логів
```
Database (optional):

```bash
docker exec -it postgres psql -U postgres
```
