## Цель
Научиться упаковывать FastAPI приложение в Docker, интегрировать парсер данных с базой данных и вызывать парсер через API и очередь

## Задача
Необходимо создать Dockerfile для упаковки FastAPI приложения и приложения с паресером. В Dockerfile указать базовый образ, установить необходимые зависимости, скопировать исходные файлы в контейнер и определить команду для запуска приложения.

Главное main-app
```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
```

Парсер
```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Создание Docker Compose файла
Необходимо написать docker-compose.yml для управления
оркестром сервисов, включающих FastAPI приложение, 
базу данных и парсер данных. Определите сервисы, 
укажите порты и зависимости между сервисами.

Докер компоуз
```dockerfile
version: "3.9"

services:
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: root1234
      POSTGRES_DB: bookcrossing
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7
    ports:
      - "6379:6379"

  parser:
    build: ./parser
    volumes:
      - ./parser:/app
    depends_on:
      - redis
    command: ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
    ports:
      - "8001:8000"  

  parser_worker:
    build: ./parser
    volumes:
      - ./parser:/app
    depends_on:
      - redis
    command:
      [
        "celery",
        "-A",
        "celery_app.celery_app",
        "worker",
        "--loglevel=info",
        "-Q",
        "parsing"
      ]

  web_app:
    build: ./web_app
    env_file:
      - ./web_app/.env
    depends_on:
      - db
      - parser
    volumes:
      - ./web_app:/app
    ports:
      - "8000:8000"
    command:
      [
        "uvicorn",
        "main:app",
        "--host",
        "0.0.0.0",
        "--port",
        "8000",
        "--reload"
      ]

volumes:
  postgres_data:
```


