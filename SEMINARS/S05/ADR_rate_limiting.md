# ADR: Adaptive HH.ru API Rate Limiting
Status: Proposed

## Context
Risk: R-02 (L=4, I=4, Score=16) - Блокировка аккаунта из-за превышения лимитов HH.ru API
DFD: Edge Business Logic → HH.ru API
NFR: NFR-002
Assumptions: Лимиты HH.ru API ∼200 запросов/день, массовые автоматические отклики

## Decision
Реализовать адаптивную систему rate limiting с очередью и мониторингом использования квот. Система должна динамически адаптироваться к ответам HH.ru API.

- **Adaptive limiting:** автоматическая регулировка на основе headers HH.ru (X-RateLimit-*)
- **Очередь откликов:** отложенная обработка при превышении лимитов
- **Мониторинг квот:** отслеживание использования в реальном времени
- **Уведомления:** предупреждения при достижении 80% лимита
- **Scope:** все вызовы HH.ru API

## Alternatives
- **Простой rate limiting** - не адаптируется к изменениям политик HH.ru
- **Полная очередь** - излишняя сложность для текущих потребностей

## Consequences
+ Предотвращение блокировки аккаунтов
+ Автоматическая адаптация к изменениям лимитов
- Усложнение архитектуры
- Задержки при обработке в очереди

## DoD / Acceptance
Given приближение к лимитам HH.ru API
When система отправляет отклики
Then автоматически регулирует частоту или ставит в очередь

Checks:
- test: adaptive limiting корректно реагирует на 429 ответы
- log: фиксация событий регулировки и постановки в очередь
- metric: 0 блокировок аккаунтов из-за превышения лимитов
- SLO: задержка в очереди ≤ 30 минут

## Rollback / Fallback
Отключение adaptive limiting через конфиг, переход на фиксированный rate limit. Мониторинг количества 429 ошибок.

## Trace
- DFD: Edge Business Logic → HH.ru API
- STRIDE: строка 13 (Denial of Service threat)
- Risk scoring: R-02, Top-2
- NFR: NFR-002
- Issues: Rate limiting implementation

## Ownership & Dates
Owner: Backend Team  
Reviewers: DevOps Team  
Date created: 2025-01-15  
Last updated: 2025-01-15

## Open Questions
- Точные лимиты HH.ru API для разных типов запросов
- Стратегия приоритизации запросов в очереди