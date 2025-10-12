# Матрица STRIDE per element для системы автоматизации поиска работы

| Element | Data/Boundary | Threat (S/T/R/I/D/E) | Description | NFR link (ID) | Mitigation idea (ADR later) |
| ------- | ------------- | -------------------- | ----------- | ------------- | --------------------------- |
| Edge: Internet → Spring Boot Controller | OAuth токен, профиль | S | Подмена OAuth токена или учетных данных пользователя при авторизации через HH.ru | NFR-005 | OAuth 2.0 с PKCE, валидация state параметра |
| Edge: Internet → Spring Boot Controller | Профиль, фильтры | T | Изменение данных профиля или фильтров поиска злоумышленником | NFR-009 | HMAC подпись критичных данных, валидация на стороне сервера |
| Edge: Internet → Spring Boot Controller | Webhook события | R | Отрицание получения webhook событий от Telegram | NFR-007 | Аудит всех входящих webhook с timestamp и signature |
| Edge: Spring Boot Controller → Business Logic | DTO/Запросы | I | Утечка PII данных между слоями приложения | NFR-006 | Маскирование PII в логах, шифрование чувствительных полей |
| Node: Business Logic | Обработка запросов | D | DoS атака через ресурсоемкие операции поиска вакансий | NFR-001, NFR-002 | Rate limiting, кэширование результатов поиска |
| Node: Business Logic | Авторизация | E | Обход проверок прав доступа к данным других пользователей | need NFR | Strict RBAC, проверка владения ресурсом на каждом уровне |
| Edge: Business Logic → PostgreSQL | SQL запросы | T | Изменение данных в БД через SQL инъекции | NFR-009 | Параметризованные запросы, ORM с экранированием |
| Node: PostgreSQL | Хранение данных | I | Несанкционированный доступ к PII данным пользователей | NFR-006 | Шифрование PII полей, ограничение доступа к БД |
| Edge: Business Logic → Redis | Кэш токенов | I | Утечка OAuth токенов из кэша | NFR-005 | Шифрование токенов в Redis, короткое TTL |
| Node: Redis | Хранение сессий | D | Исчерпание памяти через атаку на кэш | need NFR | Ограничение размера кэша, мониторинг использования памяти |
| Edge: Business Logic → Ollama Integration | Запросы генерации | T | Подмена промптов или данных для ИИ-генерации | NFR-003 | Валидация входных данных, санитизация промптов |
| Node: Ollama Integration | Генерация контента | I | Утечка PII через ИИ-модель в логах | need NFR | Очистка PII из запросов к ИИ, отдельное логгирование |
| Edge: Business Logic → HH.ru API | Запросы откликов | D | Блокировка аккаунта из-за превышения лимитов API | NFR-002 | Динамический rate limiting, мониторинг лимитов HH.ru |
| Edge: HH.ru API → Business Logic | Входящие сообщения | T | Подмена сообщений от рекрутеров | NFR-009 | Верификация подлинности сообщений через API HH.ru |
| Node: HH.ru API | Внешний сервис | D | Недоступность HH.ru API влияет на основной функционал | NFR-008 | Circuit breaker, graceful degradation |
| Edge: Business Logic → Telegram Bot API | Уведомления | I | Утечка PII через уведомления в Telegram | NFR-006 | Маскирование PII в уведомлениях, настройки приватности |
| Edge: Telegram Bot API → Business Logic | Webhook события | S | Подмена webhook событий от Telegram | NFR-005 | Верификация webhook через secret token |
| Node: Spring Boot Controller | Обработка запросов | D | DoS через массовые запросы к API | NFR-001, NFR-002 | Global rate limiting, WAF protection |
| Edge: Business Logic → Business Logic | Внутренние вызовы | R | Отсутствие аудита бизнес-операций | NFR-007 | Сквозное логгирование с correlation_id |
| Node: Весь сервис | Конфигурация | E | Доступ к чувствительной конфигурации (API keys) | need NFR | Secrets management, разделение прав доступа |