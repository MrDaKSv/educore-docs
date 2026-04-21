# Test Strategy — EduCore Platform

**Version:** v1.0  
**Status:** Approved  
**Last updated:** 2026  

> References: [SSD — System Specification](../architecture/SSD.md) | [Traceability Matrix](traceability-matrix.md)

---

# 1. Testing Objectives

Метою тестування системи EduCore є перевірка коректності роботи всіх функціональних модулів освітньої платформи, забезпечення стабільності, безпеки та відповідності вимогам, визначеним у SSD.

Основні цілі:

- Перевірка всіх функціональних вимог (FR-01.01 – FR-06.01)
- Валідація нефункціональних вимог (продуктивність, безпека, доступність)
- Досягнення тестового покриття ≥ 80% для unit-тестів
- Забезпечення стабільної роботи мікросервісної архітектури

---

# 2. Testing Levels

## Unit Testing

Мета: перевірка окремих компонентів системи.

Охоплення:
- User Service (валідація користувачів)
- Course Service (логіка курсів)
- Assignment Service (обробка завдань)
- Grading Service (оцінювання)
- File Storage Service (файли)

Інструменти:
- JUnit (Java)
- Jest (якщо є frontend або Node.js частина)

```bash
mvn test
npm run test
```
### Integration Testing

Мета: перевірка взаємодії між мікросервісами.

Охоплення: API Gateway → Service → Database Redis кешування взаємодія між сервісами (User → Course → Assignment)

Інструменти: Spring Boot Test + Testcontainers(PostgreSQL, Redis)

```bash
mvn verify
```
### End-to-End (E2E) Testing

**Мета:** перевірка повних користувацьких сценаріїв у системі EduCore через HTTP API та UI.

**Сценарії:**
- Реєстрація → Логін → Доступ до курсу
- Викладач: створення курсу → додавання матеріалів → створення завдання
- Студент: перегляд курсу → завантаження завдання → отримання оцінки
- Викладач: перевірка роботи → виставлення оцінки
- Спроба доступу без авторизації → відхилення (401 Unauthorized)

**Інструменти:**
- Postman Collections
- Selenium (для UI тестування, якщо використовується frontend)

---

### Security Testing

**Мета:** перевірка захисту системи EduCore від типових атак та вразливостей.

**Перевірки:**
- Захист від brute-force атак (rate limiting через API Gateway)
- Стійкість до SQL injection
- Перевірка JWT:
  - підробка токена (tampering)
  - прострочені токени
  - неправильний підпис
- Перевірка RBAC (Student / Teacher / Admin доступи)
- Захист від XSS (валідація введених даних)
- Перевірка безпечного завантаження файлів (тип, розмір, malware scanning)

**Інструменти:**
- OWASP ZAP
- Postman (manual security testing)
- Browser DevTools (перевірка cookie, headers)

### Performance Testing

**Мета:** перевірка відповідності нефункціональним вимогам, визначеним у [SSD](../architecture/SSD.md#4-non-functional-requirements).

**Цілі:**
- ≥ 1000 запитів/хв
- p95 response time ≤ 300 ms
- стабільна робота при одночасному навантаженні
- перевірка масштабування (horizontal scaling)

**Інструмент:** k6

```javascript
// k6 load test scenario for EduCore
export const options = {
  vus: 50,
  duration: '1m',
  thresholds: {
    http_req_duration: ['p(95)<300'], // час відповіді
    http_reqs: ['rate>16.7'], // 1000 rpm ≈ 16.7 rps
  },
};

export default function () {
  const urls = [
    'http://localhost:8080/api/users/login',
    'http://localhost:8080/api/courses',
    'http://localhost:8080/api/assignments/submit',
  ];

  urls.forEach(url => {
    http.get(url);
  });
}
```
---

## 3. Tools Summary

| Tool | Purpose |
|------|--------|
| JUnit | Unit тестування backend (Spring Boot) |
| Spring Boot Test | Integration testing |
| Postman | HTTP API testing |
| Selenium | UI testing (для frontend) |
| k6 | Load / performance testing |
| OWASP ZAP | Security scanning |
| GitHub Actions | CI — автоматичний запуск тестів при push |

---

## 4. Test Environment

- **Unit/Integration:** локальне середовище або CI (Docker Compose з PostgreSQL + Redis)
- **E2E:** staging середовище (production-like конфігурація)
- **Performance:** ізольоване staging середовище (щоб не впливати на продакшн)
- **Security:** staging середовище (OWASP ZAP автоматичне сканування)

---

## 5. Definition of Done

Функціональність вважається протестованою, якщо:

1. Unit тести проходять з покриттям ≥ 80%.
2. Integration тести проходять для основних сценаріїв (happy path) та помилок.
3. Тести пов’язані з вимогами через [Traceability Matrix](traceability-matrix.md).
4. Відсутні Critical або High рівня вразливості.
5. Система відповідає нефункціональним вимогам (продуктивність, стабільність).

---

*Цей документ описує підхід до тестування системи EduCore. Конкретні тест-кейси та зв’язок з вимогами визначені у [Traceability Matrix](traceability-matrix.md).*
