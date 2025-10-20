# ADR: External API Resilience Pattern
Status: Approved

## Context
Risk: R-04 (L=3, I=4, Score=12) - Недоступность внешних API (HH.ru, Telegram)
DFD: Edge Business Logic → External APIs
NFR: NFR-008
Assumptions: Критическая зависимость от внешних API, возможны временные сбои, требуется высокая доступность

## Decision
Реализовать комплексный паттерн устойчивости для внешних API, включающий circuit breaker, retry механизм с джиттером и стратегическое кэширование.

- **Circuit breaker:** открытие при 50% ошибок за 1 минуту, timeout 10 секунд
- **Retry policy:** до 3 попыток с exponential backoff (1s, 2s, 4s) + джиттер ±20%
- **Кэширование:** TTL 5 минут для данных вакансий, 1 час для статических данных
- **Graceful degradation:** отключение не критичного функционала при длительных сбоях
- **Scope:** все вызовы HH.ru API и Telegram Bot API

## Alternatives
- **Полная асинхронная очередь** - излишняя сложность для текущих потребностей
- **Простые retry** - недостаточная устойчивость при длительных сбоях

## Consequences
+ Повышение доступности системы при сбоях внешних API
+ Автоматическое восстановление после временных сбоев
- Усложнение логики обработки ошибок
- Задержки при срабатывании circuit breaker

## DoD / Acceptance
Given сбой HH.ru API
When система пытается получить вакансии
Then circuit breaker активируется после 3 неудачных попыток

Checks:
- test: circuit breaker корректно открывается/закрывается
- log: фиксация событий circuit breaker и retry попыток
- metric: время восстановления внешних API ≤ 5 минут
- SLO: доступность системы ≥ 99.5% даже при сбоях внешних API

## Rollback / Fallback
Отключение circuit breaker через feature flag, переход на простые retry. Мониторинг ошибок внешних API.

## Trace
- DFD: Edge Business Logic → External APIs
- STRIDE: строка 4 (Denial of Service threat)
- Risk scoring: R-04, Top-4
- NFR: NFR-008
- Issues: External API resilience implementation

## Ownership & Dates
Owner: Backend Team  
Reviewers: DevOps Team  
Date created: 2025-01-15  
Last updated: 2025-01-15

## Open Questions
- Политика кэширования для различных типов данных
- Стратегия уведомления пользователей при деградации функционала
