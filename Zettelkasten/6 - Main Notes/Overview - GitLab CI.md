
19-06-2026 08:44

Status:

Tags: [[GitLab CI]] [[CI & CD]] [[Java+]]

---
# Overview - GitLab CI



**GitLab CI** — это CI/CD-сервис, встроенный в GitLab, который автоматически запускает сценарии сборки, тестирования и развёртывания при изменении кода.

## Ключевые компоненты

| Компонент            | Описание                                                                      |
| -------------------- | ----------------------------------------------------------------------------- |
| **`.gitlab-ci.yml`** | Конфигурационный файл в корне репозитория, описывающий весь pipeline          |
| **GitLab Runner**    | Отдельная служба, выполняющая задачи из `.gitlab-ci.yml` в Docker-контейнерах |
| **Pipeline**         | Сгруппированный по стадиям набор задач (jobs), запускающий скрипты            |
| **Job**              | Центра́льный элемент — отдельная задача с скриптами                           |
| **Stage**            | Этап pipeline, группирующий jobs                                              |

## 🔄 Как работает


```
Push в репозиторий
    ↓
GitLab обнаруживает .gitlab-ci.yml
    ↓
GitLab находит Runner (подключённый или shared)
    ↓
Runner обновляет исходники и выполняет команды
    ↓
Runner возвращает результаты в GitLab
    ↓
Результаты видны в Pipelines/Jobs вкладках
```

## 📋 Структура `.gitlab-ci.yml`

```
# Контейнер для pipeline
image: alpine:latest

# Стадии (группы задач)
stages:
  - build
  - test
  - deploy

# Кэш сборки
cache:
  paths:
    - ./*.txt

# Задача (job)
build_job:
  stage: build
  only:
    - master          # только ветка master
  script:             # Shell-скрипты
    - echo "author" > author.txt
  artifacts:          # Файлы после выполнения
    paths:
      - author.txt

# Задача с ручным запуском
deploy_job:
  stage: deploy
  when: manual        # запуск по клику в интерфейсе
  script:
    - echo "deploy"
```

## 🛠 Основные директивы

| Директива       | Описание                                          |
| --------------- | ------------------------------------------------- |
| `image`         | Docker-образ для запуска jobs                     |
| `services`      | Дополнительные Docker-сервисы                     |
| `variables`     | Переменные окружения                              |
| `stages`        | Список стадий pipeline                            |
| `script`        | Shell-скрипты задачи                              |
| `before_script` | Скрипты перед главным script                      |
| `after_script`  | Скрипты после главного script                     |
| `artifacts`     | Файлы для скачивания после job                    |
| `cache`         | Кэш между задачами                                |
| `only`/`except` | Ветки для запуска/игнорирования                   |
| `when`          | Условия запуска: `manual`, `always`, `on_failure` |
| `tags`          | Фильтр раннеров по тегам                          |

## ⚙️ Типичный pipeline для Java/microservices

```yaml
image: gradle:8-jdk-21

stages:
  - build
  - test
  - lint
  - docker-build
  - deploy

before_script:
  - ./gradlew --version

build:
  stage: build
  script:
    - ./gradlew build -x test
  artifacts:
    paths:
      - build/libs/*.jar

test:
  stage: test
  script:
    - ./gradlew test

lint:
  stage: lint
  script:
    - ./gradlew checkstyleMain checkstyleTest

docker-build:
  stage: docker-build
  script:
    - docker build -t myapp:$CI_COMMIT_SHA .
    - docker push myapp:$CI_COMMIT_SHA

deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/
  when: manual
  only:
    - master
```

## 💡 Лучшие практики

- **Интеграция в CI/CD** — запускать линтеры и тесты автоматически при каждом пуше
    
- **Изоляция в Docker** — каждая задача выполняется в отдельном контейнере
    
- **Кэширование** — использовать `cache` для зависимостей (Gradle, Maven)
    
- **Последовательность стадий** — стадия не запускается, если предыдущая ошиблась
    
- **Ручные запуски** — `when: manual` для деплоя на продакшн
    
- **Artifacts** — сохранять результаты сборки для последующих стадий



----
#### [[Overview - GitLab CI - Flashcards|Link to flashcards]]



---
### References:

