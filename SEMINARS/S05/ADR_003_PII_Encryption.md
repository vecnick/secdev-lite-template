# ADR: Application-Level PII Encryption
Status: Approved

## Context
Risk: R-03 (L=3, I=5, Score=15) - Несанкционированный доступ к PII данным пользователей
DFD: Node PostgreSQL
NFR: NFR-006
Assumptions: Хранение резюме, контактов, истории откликов - GDPR sensitive data

## Decision
Реализовать шифрование PII данных на уровне приложения с использованием ключей из защищенного хранилища. Дополнительно настроить маскирование в логах.

- **Шифруемые поля:** имя, email, телефоны, резюме, история сообщений
- **Алгоритм:** AES-256-GCM
- **Key management:** HashiCorp Vault или AWS KMS
- **Маскирование в логах:** автоматическое для всех PII полей
- **Scope:** все PII данные в БД и логах

## Alternatives
- **Шифрование на уровне БД** - сложнее в отладке, зависимость от вендора БД
- **Только маскирование в логах** - недостаточная защита данных в БД

## Consequences
+ Защита данных при компрометации БД
+ Соответствие GDPR требованиям
- Усложнение запросов к данным
- Производительность при шифровании/дешифровании

## DoD / Acceptance
Given PII данные пользователя
When сохраняются в БД или логируются
Then шифруются/маскируются соответственно

Checks:
- test: зашифрованные данные в БД не читаемы без ключа
- log: отсутствие чистого PII в логах приложения
- scan: успешная проверка на отсутствие PII в логах
- metric: время шифрования/дешифрования ≤ 50ms на операцию

## Rollback / Fallback
Постепенная миграция с feature flags. Резервное копирование незашифрованных данных.

## Trace
- DFD: Node PostgreSQL
- STRIDE: строка 8 (Information Disclosure threat)
- Risk scoring: R-03, Top-3
- NFR: NFR-006
- Issues: PII encryption implementation

## Ownership & Dates
Owner: Security Team  
Reviewers: Backend Team, DBA  
Date created: 2025-01-15  
Last updated: 2025-01-15

## Open Questions
- Стратегия миграции существующих данных
- Процедура восстановления при потере ключей шифрования