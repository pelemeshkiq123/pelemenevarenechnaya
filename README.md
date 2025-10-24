```markdown
# Momo Store - Пельменная №2

Vue.js фронтенд и Go бэкенд приложение для интернет-магазина пельменей, развернутое в Kubernetes кластере Yandex Cloud.

## CI/CD Pipeline

### Стадии пайплайна

| Стадия | Описание | Инструменты |
|--------|-----------|-------------|
| **build** | Сборка Go приложения | Go 1.17 |
| **test** | Unit-тесты и статический анализ | SonarQube |
| **docker-build** | Сборка Docker образов | Docker, Kaniko |
| **release** | Тегирование в GitLab Registry | GitLab Registry |
| **deploy** | Деплой в Kubernetes | kubectl, Helm |

### Версионирование

Версии образов формируются автоматически по шаблону:

```
1.0.${CI_PIPELINE_ID}
```

Пример: `1.0.7496502`

Все образы хранятся в **GitLab Container Registry**:
- `gitlab.praktikum-services.ru:5050/std-int-005-013/varenikilife/momo-backend:1.0.7496502`
- `gitlab.praktikum-services.ru:5050/std-int-005-013/varenikilife/momo-frontend:latest`

## Качество кода

### Backend тестирование

Для Go бэкенда настроен статический анализ через **SonarQube**:

```bash
sonar-scanner \
  -Dsonar.projectKey="std-int-005-013_varenikilife_AZoVXruJB0vRiJmVN070" \
  -Dsonar.sources="." \
  -Dsonar.host.url="https://sonarqube.praktikum-services.ru" \
  -Dsonar.login="${SONAR_TOKEN}"
```

Проверяются:
- Code coverage
- Code smells
- Security vulnerabilities
- Bugs
- Technical debt

## Kubernetes Deployment

### Автоматический деплой

При мерже в ветку `main` автоматически происходит:
1. Сборка новых образов
2. Пуш в GitLab Registry
3. Деплой в production Kubernetes кластер

### Архитектура в k8s

- **Frontend**: Vue.js SPA на nginx
- **Backend**: Go API сервер
- **Ingress**: nginx controller с SSL termination
- **Services**: ClusterIP для внутренней коммуникации

### Сертификаты SSL

HTTPS сертификаты автоматически управляются через **Yandex Cloud Certificate Manager**:
- Домен: `пельменнососисочныйдомен.рф`
- Автоматическое продление Let's Encrypt
- TLS termination на ingress уровне

## Локальная разработка

### Запуск бэкенда
```bash
cd backend
go run cmd/api/main.go
```

### Запуск фронтенда
```bash
cd frontend
npm run serve
```

### Docker сборка
```bash
docker build -t momo-backend:latest -f backend/Dockerfile .
docker build -t momo-frontend:latest -f frontend/Dockerfile .
```

## Production доступ

- **URL**: https://пельменнососисочныйдомен.рф
- **API**: https://пельменнососисочныйдомен.рф/api

## Структура проекта

```
├── backend/          # Go бэкенд
├── frontend/         # Vue.js фронтенд
├── k8s/             # Kubernetes манифесты
├── .gitlab-ci.yml   # CI/CD конфигурация
└── README.md
```

## Переменные окружения GitLab

- `KUBE_CONFIG` - kubeconfig для доступа к кластеру
- `SONAR_TOKEN` - токен для SonarQube
- `CI_REGISTRY_USER` - логин для GitLab Registry
- `CI_REGISTRY_PASSWORD` - пароль для GitLab Registry

---

**Статус**: Production  
**Последний деплой**: ${CI_PIPELINE_CREATED_AT}

---

*Разработано с ❤️ для любителей пельменей*
```

Теперь все секции должны правильно отображаться в GitLab!