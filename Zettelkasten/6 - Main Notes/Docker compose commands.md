
03-04-2026 21:50

Status:

Tags: [[Docker]] [[CI & CD]]

---
# Docker compose commands


> [!tip] Note
> `docker-compose` - устаревшая tool-а.
> `docker compose` -  новая версия.

## Запуск и сборка

- `docker compose up` — собирает (если нужно) и запускает все сервисы в foreground-режиме.
    
- `docker compose up -d` — запускает в фоне (detached).
    
- `docker compose build` — собирает образы сервисов (с `--no-cache` для пересборки без кэша).
    

## Выполнение команд

- `docker compose run [сервис] [команда]` — запускает одноразовый контейнер сервиса (например, `run app bash`).
    
- `docker compose run --rm [сервис] [команда]` — автоматически удаляет контейнер после выполнения.
    

## Управление сервисами

- `docker compose down` — останавливает и удаляет контейнеры, сети, тома.
    
- `docker compose stop` — останавливает контейнеры (без удаления, можно `start` заново).
    
- `docker compose restart` — перезапускает сервисы.
    

## Мониторинг

- `docker compose ps` — показывает статус контейнеров.
    
- `docker compose logs` — выводит логи (с `-f` для следования в реальном времени).
    
- `docker compose pull` — загружает свежие образы.[

---
### Несколько compose одновременно

Несколько проектов Docker Compose можно запускать одновременно — они изолируются по имени проекта (по умолчанию на основе имени папки). Для обращения к конкретному используйте флаг `-p` (project name) или `-f` (файл compose).


- `docker compose -p myproject up -d` — запуск/остановка по имени.
    
- `docker compose -p myproject ps` — статус контейнеров.
    
- `docker compose -p myproject logs` — логи конкретного проекта.


----
#### [[Docker compose commands - Flashcards|Link to flashcards]]



---
### References:

