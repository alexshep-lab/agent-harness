> **Sanitized example.** Real `CLAUDE.md` of a conference-management platform I
> built and operate solo (client name, domains, IPs and access details replaced
> with placeholders; structure and content otherwise verbatim). The **Known
> gotchas** section is the point: every production incident becomes a permanent
> constraint that agents read before touching anything.
>
> **TL;DR (EN).** Codebase context of a 17-app Django 5.2 conference platform
> run solo in production: stack, production topology (VPS, Gunicorn/Nginx,
> systemd auto-restart policy, cron backups with rotation), per-app
> architecture map, deploy commands — and hard-won constraints, e.g.
> PostgreSQL is blacklisted from unattended upgrades because an auto-upgrade
> once took the database down (incident 2026-05-22), with the restart policy
> pinned to the versioned systemd unit, not the oneshot meta-unit.

# Sphere — Система управления конференциями

## Обзор проекта
- Django 5.2, Python 3.10+ (dev: 3.13, prod: 3.12), Bootstrap 5
- Платформа для организации научных конференций, семинаров и мероприятий
- База данных: SQLite (dev), PostgreSQL (prod) — переключается через USE_POSTGRES в .env
- Деплой: VPS, Gunicorn + Nginx; Production URL: `https://<prod-domain>`

## Production
- Сервер: VPS, Ubuntu 24.04 LTS (мигрирован со старого сервера 2026-05)
- Python 3.12, PostgreSQL 16, Nginx 1.24
- Gunicorn: workers = `cpu_count*2+1` на 127.0.0.1:8000, systemd-сервис
- SSL: Let's Encrypt (certbot), автообновление
- Обновление: `deploy/update.sh` (git pull + pip install + migrate + collectstatic + restart)
- Auto-restart: `Restart=always` на app/nginx (drop-in units). PostgreSQL —
  drop-in `Restart=on-failure` на `postgresql@16-main.service` (НЕ на
  `postgresql.service` — это `Type=oneshot` мета-юнит, `Restart=` для него запрещён).
- `postgresql-*` и `libpq*` исключены из unattended-upgrades (`Package-Blacklist`) —
  автоапгрейд минорной версии ронял dpkg и оставлял БД лежать (инцидент
  2026-05-22). PostgreSQL обновлять вручную при деплое.
- Бэкапы: скрипт через cron ежедневно в 03:00 (PG dump, /var/www, /etc/{nginx,letsencrypt,systemd}),
  ротация 14 дней.

## Архитектура

### Django-приложения (17 модулей)
- `accounts` — аутентификация, профили, OAuth (3 провайдера), MaintenanceMode, единый `/me/`
- `events` — управление мероприятиями (ядро), гранулярные права секций, калькулятор стоимости
- `registration` — регистрация участников (legacy + primary-форма), proxy-регистрация
- `submissions` — подача докладов (Call for Papers)
- `schedule` — расписание с Drag & Drop
- `tasks` — задачи и Kanban-доски
- `invitations` — приглашения с QR-кодами
- `analytics` — статистика и отчёты
- `custom_forms` — динамический конструктор форм (15 типов полей, условная логика,
  primary-форма, proxy-режим, ranking с подсчётом Борда)
- `integrations` — Telegram-уведомления оргкомитету
- `emails` — email-рассылки и шаблоны
- `tagging` — теги и автоправила
- `partners` — партнёры и договоры
- `crm` — CRM / People Hub
- `program` — программа-сетка (дни, группы, блоки, drag-and-drop, версионирование)
- `documents` — файловое хранилище per-event с 4 уровнями доступа
- `knowledge_base` — база знаний / FAQ

## Соглашения по коду
- Views: Django CBV с LoginRequiredMixin
- Forms: crispy_forms с bootstrap5 template pack
- Шаблоны: templates/{app_name}/
- **Каждое приложение имеет `NOTEBOOK_LLM.md` — контекст модуля для LLM**
- Логирование: `logging.getLogger('{app_name}')` в каждом приложении
- Кастомная модель пользователя: `accounts.User` (AUTH_USER_MODEL)

## Known gotchas
- **SECURE_PROXY_SSL_HEADER**: python-decouple возвращает строку, а Django ожидает
  tuple. В settings реализован кастомный парсинг `HTTP_X_FORWARDED_PROTO,https` →
  tuple. Без этого при SECURE_SSL_REDIRECT=True возникает 500 (бесконечный редирект).
- **Nginx `^~` для ACME**: `location ~ /\.` (deny dotfiles) имеет приоритет над
  обычным prefix-location. Для certbot нужен `location ^~ /.well-known/acme-challenge/`.
- **MaintenanceMode**: singleton-модель, middleware кэширует статус на 30 сек —
  после переключения возможна задержка до 30 секунд. Staff и `/admin/` всегда пропускаются.
- **organizers.filter**: Event.organizers — M2M через промежуточную модель.
  Фильтрация: `event.organizers.filter(pk=user.pk)`, НЕ `filter(user=user)` (FieldError).
- **date vs datetime**: свойства `is_registration_open` / `is_cfp_open` сравнивают
  с `.date()`, не с `timezone.now()` напрямую (DateField vs datetime).

## Запреты
- НЕ модифицировать файлы миграций вручную
- НЕ менять AUTH_USER_MODEL (accounts.User глубоко интегрирован)
- НЕ удалять NOTEBOOK_LLM.md из приложений
- НЕ коммитить .env, db.sqlite3, logs/, media/, staticfiles/
- НЕ менять порядок MIDDLEWARE и INSTALLED_APPS без понимания зависимостей
