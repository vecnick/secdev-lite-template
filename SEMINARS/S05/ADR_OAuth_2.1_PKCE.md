# ADR: OAuth 2.1 PKCE Implementation
Status: Proposed

## Context
Risk: R-01 (L=4, I=5, Score=20) - Подмена OAuth токена при авторизации через HH.ru
DFD: Edge Internet → Spring Boot Controller
NFR: NFR-005
Assumptions: Интеграция с HH.ru OAuth 2.0, публичный эндпоинт, Spring Security stack

## Decision
Реализовать OAuth 2.1 с PKCE (Proof Key for Code Exchange) для защиты от атак подмены и перехвата токенов. Установить короткий TLL access token и обязательную валидацию state параметра.

- **PKCE flow:** code_challenge + code_verifier для всех клиентов
- **Access token TTL:** 15 минут
- **Refresh token TTL:** 30 дней
- **State validation:** обязательная проверка с хранением в сессии
- **Scope:** все эндпоинты аутентификации `/auth/*`

## Alternatives
- **mTLS для клиентов** - слишком сложно для веб-клиентов, требует инфраструктуры PKI
- **Только короткий TTL** - недостаточная защита от replay атак

## Consequences
+ Защита от interception и replay атак
+ Соответствие OAuth 2.1 best practices
- Требует изменения клиентской логики
- Усложняет flow аутентификации

## DoD / Acceptance
Given пользователь начинает OAuth flow
When выполняется авторизация через HH.ru
Then используется PKCE (code_challenge) и проверяется state параметр

Checks:
- test: OAuth flow с PKCE проходит успешно
- log: фиксация использования PKCE в аудит-логах
- scan: отсутствие передачи code_verifier в redirect URI
- metric: 100% успешных аутентификаций используют PKCE

## Rollback / Fallback
Временное отключение PKCE через feature flag. Мониторинг ошибок аутентификации.

## Trace
- DFD: Edge Internet → Spring Boot Controller
- STRIDE: строка 1 (Spoofing threat)
- Risk scoring: R-01, Top-1
- NFR: NFR-005
- Issues: OAuth security implementation

## Ownership & Dates
Owner: Security Team  
Reviewers: Backend Team  
Date created: 2025-01-15  
Last updated: 2025-01-15

## Open Questions
- Совместимость PKCE с текущей версией HH.ru API
- Миграционная стратегия для существующих пользователей
