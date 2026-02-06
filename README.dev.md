# Strapi Development Setup 🚀

Быстрый старт для локальной разработки Strapi с Docker.

## Требования

- Docker Desktop (или Docker Engine + Docker Compose)
- Минимум 4GB RAM
- Минимум 10GB свободного места на диске

## Быстрый старт

```bash
# 1. Скопируй пример .env и настрой при необходимости
cp .env.example .env

# 2. Запусти всё одной командой
docker-compose up

# 3. Жди пока установятся зависимости (первый раз ~5-10 минут)
# Логи покажут когда готово: "Server running on http://0.0.0.0:1337"

# 4. Открой браузер
# Админка: http://localhost:1337/admin
# API: http://localhost:1337/api
```

Вот и всё! 🎉

## Что запустилось?

- **PostgreSQL** (порт 5432) — база данных
- **Strapi** (порт 1337) — CMS в development режиме с hot reload

## Полезные команды

```bash
# Запустить в фоне
docker-compose up -d

# Посмотреть логи
docker-compose logs -f strapi

# Остановить
docker-compose down

# Остановить и удалить все данные (БД тоже!)
docker-compose down -v

# Перезапустить только Strapi
docker-compose restart strapi

# Зайти в контейнер Strapi
docker-compose exec strapi sh

# Установить новую зависимость
docker-compose exec strapi yarn add package-name
```

## Структура проекта

```
strapi/
├── docker-compose.yml     # Конфигурация Docker
├── .env                   # Переменные окружения (создай из .env.example)
├── .env.example          # Шаблон переменных
├── README.dev.md         # Этот файл
└── ... (исходники Strapi)
```

## Частые проблемы

### 🐛 Порт 1337 уже занят

```bash
# Останови процесс на порту 1337
lsof -ti:1337 | xargs kill -9

# Или измени порт в docker-compose.yml:
ports:
  - "3000:1337"  # теперь доступ через localhost:3000
```

### 🐛 Порт 5432 уже занят (PostgreSQL)

```bash
# Останови локальный PostgreSQL
brew services stop postgresql  # macOS
sudo service postgresql stop   # Linux

# Или измени порт в docker-compose.yml:
ports:
  - "5433:5432"
environment:
  DATABASE_PORT: 5432  # оставляем 5432 внутри контейнера
```

### 🐛 "node_modules not found" или зависимости не устанавливаются

```bash
# Пересобери контейнер без кеша
docker-compose down
docker-compose up --build

# Или вручную установи зависимости
docker-compose exec strapi yarn install
```

### 🐛 Изменения кода не применяются (нет hot reload)

```bash
# Проверь что volumes правильно настроены
docker-compose down
docker-compose up
```

### 🐛 Ошибки миграций БД

```bash
# Удали БД и начни с чистого листа
docker-compose down -v
docker-compose up
```

### 🐛 Контейнер падает с "out of memory"

Увеличь лимит памяти в Docker Desktop:
- Settings → Resources → Memory → минимум 4GB

## Генерация секретных ключей

Если нужно обновить ключи в `.env`:

```bash
# Генерация случайного ключа
node -e "console.log(require('crypto').randomBytes(16).toString('base64'))"

# Генерация 4 ключей для APP_KEYS
node -e "console.log(Array(4).fill().map(() => require('crypto').randomBytes(16).toString('base64')).join(','))"
```

## Production deploy

⚠️ **Для production используй:**
- Отдельный `docker-compose.prod.yml`
- `NODE_ENV=production`
- Secure secrets (не оставляй дефолтные!)
- Reverse proxy (nginx/caddy)
- Backup стратегию для БД

## Документация

- [Strapi Documentation](https://docs.strapi.io/)
- [Docker Compose docs](https://docs.docker.com/compose/)
- [PostgreSQL docs](https://www.postgresql.org/docs/)

## Нужна помощь?

1. Проверь логи: `docker-compose logs -f`
2. Проверь что контейнеры запущены: `docker-compose ps`
3. Проверь `.env` файл создан и заполнен

---

**Удачной разработки!** 🚀
