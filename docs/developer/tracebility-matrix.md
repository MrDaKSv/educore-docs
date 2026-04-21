# Traceability Matrix — EduCore Platform

**Version:** v1.0  
**Last updated:** 2026  

> Requirements source: [SSD](../architecture/SSD.md)  
> Test approach: [Test Strategy](test-strategy.md)

---

# Requirements ↔ Test Cases Mapping

| Requirement | Description | Test Case | Type | Status |
|-------------|-------------|-----------|------|--------|
| FR-01.01 | Реєстрація користувача | TC-01 | Integration | To be tested |
| FR-01.02 | Авторизація (JWT) | TC-02 | Integration | To be tested |
| FR-01.03 | Управління ролями | TC-03 | Security | To be tested |
| FR-02.01 | Створення курсу | TC-04 | Integration | To be tested |
| FR-02.02 | Управління модулями | TC-05 | Integration | To be tested |
| FR-02.03 | Призначення викладача | TC-06 | Integration | To be tested |
| FR-03.01 | Перегляд матеріалів | TC-07 | E2E | To be tested |
| FR-04.01 | Завантаження завдання | TC-08 | Integration | To be tested |
| FR-04.02 | Збереження відповідей | TC-09 | Integration | To be tested |
| FR-05.01 | Перевірка робіт | TC-10 | Integration | To be tested |
| FR-05.02 | Виставлення оцінок | TC-11 | Integration | To be tested |
| FR-06.01 | Завантаження файлів | TC-12 | Integration | To be tested |
| NFR-01 | Performance (1000 rpm, ≤300ms) | TC-13 | Performance | To be tested |
| NFR-02 | Безпека (JWT, HTTPS, RBAC) | TC-14 | Security | To be tested |

---

# Test Case Descriptions

---

### TC-01 — Реєстрація користувача

**Requirement:** FR-01.01  
**Type:** Integration  

**Steps:**
1. Відправити `POST /api/users/register` з валідними даними  
2. Перевірити статус `201 Created`  
3. Перевірити наявність користувача в БД  

**Expected result:** користувач створений

---

### TC-02 — JWT авторизація

**Requirement:** FR-01.02  
**Type:** Integration  

**Steps:**
1. Відправити `POST /api/users/login`  
2. Перевірити `200 OK`  
3. Перевірити наявність JWT токена  

**Expected result:** користувач авторизований

---

### TC-03 — RBAC перевірка

**Requirement:** FR-01.03  
**Type:** Security  

**Steps:**
1. Увійти як Student  
2. Спробувати створити курс  
3. Перевірити `403 Forbidden`  

**Expected result:** доступ заборонений

---

### TC-04 — Створення курсу

**Requirement:** FR-02.01  
**Type:** Integration  

**Steps:**
1. Увійти як Teacher  
2. Відправити `POST /api/courses`  
3. Перевірити `201 Created`  

**Expected result:** курс створено

---

### TC-05 — Управління модулями

**Requirement:** FR-02.02  
**Type:** Integration  

**Steps:**
1. Додати модуль до курсу  
2. Перевірити збереження  

**Expected result:** модуль додано

---

### TC-06 — Призначення викладача

**Requirement:** FR-02.03  
**Type:** Integration  

**Steps:**
1. Admin призначає викладача  
2. Перевірити зміну  

**Expected result:** викладач призначений

---

### TC-07 — Перегляд матеріалів

**Requirement:** FR-03.01  
**Type:** E2E  

**Steps:**
1. Студент відкриває курс  
2. Переглядає матеріали  

**Expected result:** матеріали доступні

---

### TC-08 — Завантаження завдання

**Requirement:** FR-04.01  
**Type:** Integration  

**Steps:**
1. Студент завантажує файл  
2. Перевірити відповідь  

**Expected result:** завдання прийнято

---

### TC-09 — Збереження відповідей

**Requirement:** FR-04.02  
**Type:** Integration  

**Steps:**
1. Перевірити запис у БД  
2. Перевірити відповідність  

**Expected result:** відповідь збережена

---

### TC-10 — Перевірка робіт

**Requirement:** FR-05.01  
**Type:** Integration  

**Steps:**
1. Викладач відкриває роботу  
2. Перевіряє її  

**Expected result:** робота доступна для перевірки

---

### TC-11 — Виставлення оцінок

**Requirement:** FR-05.02  
**Type:** Integration  

**Steps:**
1. Викладач ставить оцінку  
2. Перевірити збереження  

**Expected result:** оцінка збережена

---

### TC-12 — Завантаження файлів

**Requirement:** FR-06.01  
**Type:** Integration  

**Steps:**
1. Завантажити файл  
2. Перевірити URL  

**Expected result:** файл доступний

---

### TC-13 — Performance тест

**Requirement:** NFR-01  
**Type:** Performance  

**Steps:**
1. Запустити k6  
2. Перевірити rpm та latency  

**Expected result:** ≥1000 rpm, ≤300 ms

---

### TC-14 — Security тест

**Requirement:** NFR-02  
**Type:** Security  

**Steps:**
1. Спроба доступу без JWT  
2. Спроба доступу з чужою роллю  

**Expected result:** доступ заборонений

---

# Coverage Summary

| Requirement Group | Total | Test Cases | Coverage |
|------------------|------|-----------|----------|
| Functional (FR) | 12 | TC-01 – TC-12 | 100% |
| Non-Functional (NFR) | 2 | TC-13 – TC-14 | 100% |
| **Total** | **14** | **14** | **100%** |
