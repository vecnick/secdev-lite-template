# Build stage
FROM gradle:8.7-jdk21 AS build
WORKDIR /app
# Копируем только необходимые файлы для определения зависимостей
COPY build.gradle.kts settings.gradle.kts ./
# Копируем оставшийся исходный код
COPY --chown=gradle:gradle . .
# Устанавливаем владельца всей рабочей директории на пользователя gradle
# Это позволяет gradle-процессу создавать кэш .gradle
RUN chown -R gradle:gradle /app
# Переключаемся на пользователя gradle для выполнения сборки
USER gradle
# Теперь gradle может создать .gradle и выполнить сборку
RUN gradle dependencies --refresh-dependencies && \
    gradle build -x test --no-daemon

# Run stage
# Используем конкретную версию базового образа с минимальной сборкой
FROM openjdk:21-jdk-slim

WORKDIR /app

# Создаем непривилегированного пользователя и группу
RUN addgroup --system --gid 1001 appgroup && \
    adduser --system --uid 1001 --ingroup appgroup appuser

# Копируем JAR файл от имени root, но устанавливаем владельца
COPY --from=build --chown=appuser:appgroup /app/build/libs/*.jar app.jar

# Переключаемся на непривилегированного пользователя
USER appuser

# Указываем порт
EXPOSE 8081

# Добавляем HEALTHCHECK
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8081/actuator/health || exit 1

# Запускаем приложение
CMD ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]