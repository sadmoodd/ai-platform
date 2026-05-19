```markdown
# AI Platform — оркестр сервисов

## Состав

| Сервис | Порт | Описание |
|--------|------|----------|
| Nginx | 5959 (HTTPS), 5969 (HTTP) | Единая точка входа |
| Whisper API | 3534 | Транскрибация аудио |
| Whisper Web | 8000 | Веб-интерфейс загрузки аудио |
| Parser API | 3637 | Парсинг PDF в Excel |
| Parser Web | 8000 | Веб-интерфейс парсера |
| AI Researcher | 1213 | Голосовой поиск + B2B-агент |
| Yonote | 3534 | Управление проектами голосом |
| Redis | 6379 | Кэширование |
| SearXNG | 8081 | Метапоисковик |
| Ollama | 11434 | Локальная LLM (DeepSeek-R1) |

## Требования

- Ubuntu 24.04
- Docker + Docker Compose
- NVIDIA GPU + nvidia-container-toolkit
- 24 ГБ VRAM, 32 ГБ RAM

## Установка

### 1. Клонировать репозитории

```bash
mkdir /opt/ai-platform && cd /opt/ai-platform
git clone https://github.com/sadmoodd/ai-researcher.git
git clone https://github.com/sadmoodd/whisper-service-php.git
git clone https://github.com/sadmoodd/UniParser.git
```

### 2. Создать SSL-сертификат

```bash
mkdir -p nginx/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout nginx/ssl/selfsigned.key \
  -out nginx/ssl/selfsigned.crt \
  -subj "/CN=localhost"
```

### 3. Создать пароль для Ollama

```bash
htpasswd -c nginx/.ollama-passwd ollama
```

### 4. Запустить

```bash
docker compose up -d
docker compose exec ollama ollama pull deepseek-r1:latest
```

### 5. Открыть

```
https://localhost:5959
https://localhost:5959/ai_researcher/
https://localhost:5959/whisper/
https://localhost:5959/parser/
https://localhost:5959/yonote/
```

## Переменные окружения

Все секреты и URL заданы в `docker-compose.yml` в секциях `environment`. Для продакшена вынести в `.env` файл.

## Остановка

```bash
docker compose down
```
