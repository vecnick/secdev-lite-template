# Матрица сравнения вариантов для R-04 - Недоступность внешних API

## 0) Контекст риска (из S04)

* **Risk-ID:** R-04
* **Threat:** D (Denial of Service)
* **DFD element/edge:** Edge: Business Logic → External APIs
* **NFR link (ID):** NFR-008
* **L×I (1-5):** L=3, I=4, Score=12
* **Ограничения/предпосылки:** Критическая зависимость от HH.ru и Telegram API, возможны временные сбои внешних сервисов

## 1) Критерии и шкалы (1-5)

**Польза (↑):**
- **Security impact (↑):** обеспечение доступности при сбоях внешних API
- **Blast radius reduction (↑):** минимизация влияния на пользователей

**Стоимость/сложность (↓):**
- **Complexity (↓):** сложность реализации
- **Time-to-mitigate (↓):** время до внедрения
- **Dependencies (↓):** внешние зависимости

## 2) Таблица сравнения вариантов

| Alternative | Summary | Security impact (↑,1-5) | Blast radius reduction (↑,1-5) | Complexity (↓,1-5) | Time-to-mitigate (↓,1-5) | Dependencies (↓,1-5) | **Benefit** | **Cost** | **Net** | Notes |
| ----------- | ------- | ----------------------: | -----------------------------: | -----------------: | -----------------------: | -------------------: | ----------: | -------: | ------: | ----- |
| A | Circuit Breaker + Retry с джиттером + кэш | 4 | 4 | 3 | 3 | 2 | **8** | **8** | **0** | Баланс устойчивости и сложности |
| B | Полная асинхронная очередь с сагами | 5 | 5 | 5 | 5 | 4 | **10** | **14** | **-4** | Максимальная отказоустойчивость, но сложно |
| C | Простые retry (3 попытки) | 3 | 3 | 1 | 1 | 1 | **6** | **3** | **+3** | Быстро, но недостаточная устойчивость |

## 3) Тай-брейкеры при равенстве Net

* **Compliance/Privacy:** не применимо
* **Maintainability:** circuit breaker проще сопровождать чем полную асинхронную систему
* **Team fit:** есть опыт с Resilience4j/Spring Retry
* **Observability:** circuit breaker предоставляет четкие метрики состояния
* **Rollback safety:** можно отключить circuit breaker через feature flag

## 4) Решение (для переноса в ADR)

* **Chosen alternative:** A
* **Почему:** Circuit breaker с retry обеспечивает хороший баланс между устойчивостью и сложностью. Позволяет системе быстро восстанавливаться при временных сбоях внешних API.
* **ADR candidate:** "External API Resilience Pattern"
* **Связки:** Risk-ID R-04, NFR-ID NFR-008, DFD Edge Business Logic→External APIs
* **Следующие шаги:**
  1. Реализовать circuit breaker для HH.ru API
  2. Настроить retry с exponential backoff и джиттером
  3. Добавить кэширование критичных данных (вакансии, профили)
  4. Реализовать graceful degradation при длительных сбоях
