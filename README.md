# 📚 EduCore — Огляд документації

Єдине джерело істини (Single Source of Truth) для документації освітньої платформи **EduCore**.

Уся документація реалізована у підході **Documentation as Code (DaC)** та зберігається у Git-репозиторії.

---

## 📁 Структура документації EduCore

```
docs/
├── README.md ← Ви тут (точка входу)
├── architecture/
│ ├── SSD.md ← System Specification Document
│ ├── SDD.md ← Software Design Document
│ └── ISD.md ← Infrastructure Specification Document
├── quality/
│ ├── test-strategy.md ← Стратегія тестування
│ └── traceability-matrix.md ← Матриця простежуваності вимог
├── developer/
│ └── onboarding.md ← Інструкція для розробників
└── mkdocs.yml ← Конфігурація MkDocs
```

---

# 🔗 Навігація

## 🏛️ Архітектура системи

| Документ | Опис |
|----------|------|
| SSD(architecture/SSD.md) | Функціональні та нефункціональні вимоги системи |
| SDD(architecture/SDD.md) | Архітектура, компоненти, API та взаємодія сервісів |
| ISD(architecture/ISD.md) | Інфраструктура, деплой та масштабування |

---

## 🧪 Забезпечення якості

| Документ | Опис |
|----------|------|
| Test Strategy(quality/test-strategy.md) | Загальний підхід до тестування системи |
| Traceability Matrix(quality/traceability-matrix.md) | Зв’язок між вимогами та тест-кейсами |

---

## 👨‍💻 Для розробників

| Документ | Опис |
|----------|------|
| Onboarding Guide(developer/onboarding.md) | Налаштування проєкту, правила Git, старт роботи |

---

# 🏛️ Правила Single Source of Truth

| Тема | Джерело істини | Використовується в |
|------|----------------|-------------------|
| Функціональні вимоги | SSD | SDD, Test Strategy, Traceability Matrix |
| Архітектура системи | SDD | ISD, Onboarding |
| Інфраструктура | ISD | Onboarding |
| Тестування | Test Strategy | Traceability Matrix |

---

⚠️ **Правило:**
Якщо змінюється вимога — спочатку оновлюється SSD.  
Усі інші документи повинні посилатися на ідентифікатори вимог (FR-01, FR-02), а не дублювати їх.

---

# 📌 Про систему

**EduCore** — це веб-платформа для дистанційного навчання, яка дозволяє студентам проходити курси, виконувати завдання та взаємодіяти з викладачами.

Система складається з мікросервісної архітектури та REST API.

---

# ⚙️ Технології

- Java Spring Boot
- React
- PostgreSQL
- JWT (авторизація)
- Docker
- Kubernetes

---

# 📊 Версія

- Версія: `v1.0`
- Останнє оновлення: 2026
- Автор: Богдан Горбатюк
