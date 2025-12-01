# Инфраструктура PostgreSQL с мониторингом

## Описание

Этот проект содержит настройку PostgreSQL 16 с мониторингом через Prometheus и Grafana для курса "Продвинутые СУБД".

## Компоненты

- **PostgreSQL 16** - реляционная база данных
- **postgres_exporter** - экспортер метрик для Prometheus
- **Prometheus** - сбор и хранение метрик
- **Grafana** - визуализация метрик

## Требования

- Docker Desktop 24.x+
- Docker Compose v2.x+
- 4 ГБ свободного места на диске
- 4 ГБ оперативной памяти

## Быстрый старт

### 1. Запуск инфраструктуры

```bash
cd infrastructure/postgres
docker compose up -d
2. Проверка состояния
docker compose ps
Все контейнеры должны быть в статусе "Up".
3. Доступ к сервисам
PostgreSQL: localhost:5432
База данных: myapp
Пользователь: dbuser
Пароль: SecurePassword123
Grafana: http://localhost:3000
Логин: admin
Пароль: admin
Prometheus: http://localhost:9090
postgres_exporter метрики: http://localhost:9187/metrics
Подключение к PostgreSQL
Через psql в Docker
docker exec -it postgres-dev psql -U dbuser -d myapp
Через DBeaver
Создать новое подключение PostgreSQL
Host: localhost, Port: 5432
Database: myapp
Username: dbuser, Password: SecurePassword123
Структура проекта
infrastructure/
├── postgres/
│   ├── docker-compose.yml      # Оркестрация всех сервисов
│   ├── configs/
│   │   └── postgresql.conf     # Настройки PostgreSQL
│   └── init-scripts/
│       └── 01-init-db.sql      # Скрипт инициализации БД
├── monitoring/
│   └── prometheus.yml          # Конфигурация Prometheus
└── README.md                   # Эта документация
Настройки PostgreSQL
Память
shared_buffers = 512MB - общий буфер (25% RAM)
work_mem = 16MB - память на операцию
effective_cache_size = 2GB - подсказка оптимизатору
WAL
wal_level = replica - для репликации
max_wal_size = 1GB - размер до checkpoint
checkpoint_timeout = 5min - интервал checkpoint
Подключения
max_connections = 100 - максимум одновременных подключений
Мониторинг в Grafana
Импортированный dashboard: 9628 (PostgreSQL Database)
Ключевые метрики:
- TPS (Transactions Per Second) - транзакции в секунду
- Active Connections - активные подключения
- Cache Hit Ratio - процент попаданий в кэш (цель >95%)
- Database Size - размер базы данных
- Query Duration - длительность запросов
Проверка pg_stat_statements
Подключиться к PostgreSQL и выполнить:
-- Топ-10 медленных запросов
SELECT 
  substring(query, 1, 50) AS query,
  calls,
  round(total_exec_time::numeric, 2) AS total_ms,
  round(mean_exec_time::numeric, 2) AS avg_ms
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
