# ISD — Infrastructure Specification Document  
**EduCore Platform**  
Version: `v1.0`  
Status: Approved  
Last updated: 2026  

References: SDD — Software Design | SSD — System Requirements  

---

# 1. Deployment Environment

EduCore розгортається у хмарному середовищі (AWS / GCP / Azure) з використанням Docker контейнеризації та Kubernetes оркестрації.

Підтримуються три середовища:

| Environment | Purpose | Notes |
|------------|--------|------|
| development | Локальна розробка | Docker Compose, mock-сервіси |
| staging | Передпродакшн тестування | Production-like конфігурація |
| production | Жива система | High availability, monitoring enabled |

---

# 2. Infrastructure Components

| Component | Technology | Configuration |
|-----------|------------|---------------|
| Frontend | React + Nginx | Static build, CDN optional |
| Backend Services | Spring Boot (microservices) | 2+ replicas per service |
| Database | PostgreSQL 15 | Primary + Replica |
| Cache | Redis 7 | Session + caching layer |
| API Gateway | Nginx / Kong | Routing, rate limiting, SSL |
| Monitoring | Prometheus + Grafana | Metrics, dashboards |
| Logging | ELK Stack | Centralized structured logs |
| CI/CD | GitHub Actions | Build → Test → Deploy |

---

# 3. Kubernetes Configuration

```yaml id="k8s_edc_01"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: educore-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: educore-backend
  template:
    spec:
      containers:
        - name: backend
          image: educore/backend:v1.0
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_DATASOURCE_URL
              valueFrom:
                secretKeyRef:
                  name: educore-secrets
                  key: db-url
          livenessProbe:
            httpGet:
              path: /actuator/health
              port: 8080
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: 8080
  ```
# 4. Scaling Principles
- Horizontal scaling: Kubernetes HPA збільшує кількість реплік при навантаженні > 70% CPU
- Stateless backend: сервіси не зберігають стан (JWT + Redis)
- Database scaling: read replicas для оптимізації запитів
- Load balancing: Nginx розподіляє трафік між сервісами
- Health checks: /actuator/health для перевірки стану сервісів

```yaml id="k8s_edc_01"
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: educore-backend-hpa
spec:
  scaleTargetRef:
    name: educore-backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

# 5. CI/CD Pipeline

```
GitHub Push → GitHub Actions
    │
    ├── 1. Install dependencies (Maven / Gradle / npm)
    ├── 2. Run tests (JUnit + API tests)
    ├── 3. Build Docker images
    ├── 4. Push to registry (Docker Hub / GHCR)
    └── 5. Deploy to Kubernetes cluster
```

**Rollback strategy:** `kubectl rollout undo deployment/auth-service`

## 6. Monitoring and Alerting

Система EduCore використовує централізований моніторинг для контролю продуктивності, стабільності та активності користувачів у навчальній платформі.

| Metric | Alert Threshold | Action |
|--------|----------------|--------|
| Error rate API | > 1% за 5 хв | Алерт DevOps (PagerDuty / Slack) |
| Response time p95 | > 300 ms | Попередження про деградацію API |
| CPU utilization | > 70% | Автоматичне масштабування (HPA) |
| Database connections | > 80% | Оптимізація пулу з’єднань |
| Active users load | різке зростання | Аналіз навантаження системи |

---

### 📊 Grafana dashboards

- EduCore System Overview
- Course & Assignment Activity Metrics
- API Performance Dashboard
- Database Performance Monitoring
- User Engagement Statistics

## 7. Security Infrastructure

Безпека системи EduCore забезпечується на рівні автентифікації, інфраструктури та мережевої ізоляції.

---

### 🔑 7.1 Аутентифікація та авторизація

- Використовується **JWT (JSON Web Token)** для автентифікації користувачів
- Ролі системи:
  - Student
  - Teacher
  - Admin
- Доступ до API контролюється через **RBAC (Role-Based Access Control)**

---

### 🛡️ 7.2 Захист даних

- Секрети зберігаються у **Kubernetes Secrets**
- Паролі зберігаються у хешованому вигляді (BCrypt)
- Чутливі дані не передаються у відкритому вигляді

---

### 🌐 7.3 Мережна безпека

- Усі запити проходять через **HTTPS (TLS 1.2+)**
- Використовується **Nginx як reverse proxy**
- Network Policies обмежують доступ між сервісами (тільки необхідні зв’язки)

---

### ⚙️ 7.4 Інфраструктурна безпека

- Ізоляція середовищ: development / staging / production
- Контроль доступу через Kubernetes RBAC
- Автоматичні оновлення контейнерів для усунення вразливостей

---

### 🔄 7.5 CI/CD безпека

- Перевірка залежностей (dependency scanning)
- Static code analysis перед деплоєм
- Деплой дозволений тільки після успішних тестів
