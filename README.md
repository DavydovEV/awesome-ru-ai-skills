# Скиллы для работы с российскими AI-сервисами

Набор скиллов для AI-агентов (OpenClaw, Claude Code и др.), дающих доступ к российским сервисам: **GigaChat**, **YandexGPT**, **Яндекс 360**, **Yandex Cloud** и **Яндекс Метрика**.

## Что умеют

| Вы говорите | Агент делает |
|-------------|-------------|
| *«Ответь на русском через GigaChat-Max»* | Направляет запрос через прокси GigaChat |
| *«Суммаризируй файл через YandexGPT»* | Отправляет через прокси YandexGPT |
| *«Загрузи отчёт на Яндекс Диск»* | `yax disk upload` |
| *«Создай встречу завтра в 14:00»* | `yax calendar create` |
| *«Задеплой сайт в Object Storage»* | `yc storage s3 cp --recursive` |
| *«Покажи статистику Метрики за 30 дней»* | Reporting API Метрики |
| *«Добавь DNS-запись для домена»* | `yc dns zone add-records` |
| *«Настрой счётчик Метрики в Next.js»* | Генерирует компонент трекинга |

---

## Скиллы

| Скилл | Директория | Описание |
|-------|-----------|----------|
| **GigaChat** | `gigachat/` | API Сбера — модели Lite, Pro, Max через OpenAI-совместимый прокси |
| **YandexGPT** | `yandexgpt/` | Yandex Foundation Models — YandexGPT, Lite, 32K через прокси |
| **Яндекс 360** | `yax/` | Диск, Календарь, Почта (IMAP/SMTP), Телемост через CLI `yax` |
| **Yandex Cloud** | `yandex-cloud/` | Полный справочник `yc` CLI — хранилище, вычисления, DNS, serverless, БД, сертификаты и 30+ сервисов |
| **Яндекс Метрика** | `yandex-metrika/` | API аналитики — трафик, счётчики, цели, отчёты + гайд по настройке в Next.js |

---

## Установка

### ClawHub

```bash
clawhub install smvlx/openclaw-ru-skills
```

### Скрипт

```bash
git clone https://github.com/smvlx/openclaw-ru-skills.git /tmp/openclaw-ru-skills
/tmp/openclaw-ru-skills/install.sh
```

Копирует все скиллы в `~/.openclaw/skills/` и ставит зависимости.

### Вручную

```bash
git clone https://github.com/smvlx/openclaw-ru-skills.git
cp -r openclaw-ru-skills/{gigachat,yandexgpt,yax,yandex-cloud,yandex-metrika} ~/.openclaw/skills/
cd ~/.openclaw/skills/yax && npm install --omit=dev
```

### Claude Code

Для пользователей Claude Code — копируйте в `~/.claude/skills/`:

```bash
git clone https://github.com/smvlx/openclaw-ru-skills.git /tmp/openclaw-ru-skills
cp -r /tmp/openclaw-ru-skills/{yandex-cloud,yandex-metrika} ~/.claude/skills/
```

### Через чат

Вставьте ссылку на репо в диалог с агентом:

> Установи скиллы из https://github.com/smvlx/openclaw-ru-skills

---

## Настройка

Каждый скилл требует **креды от внешних сервисов** — это единственное, что делаете вы. Остальное агент настраивает сам.

### GigaChat (Сбер)

| Вы (2 мин) | Агент |
|------------|-------|
| 1. Регистрация на [developers.sber.ru](https://developers.sber.ru/) | Создаёт `.env`, ставит `gpt2giga` |
| 2. Создать приложение GigaChat API | Запускает прокси на `localhost:8443` |
| 3. Скопировать Client ID + Secret | Регистрирует провайдер |
| 4. Скоуп: `GIGACHAT_API_PERS` (бесплатно) или `_CORP` | |

**Модели:** GigaChat Lite · Pro · MAX

### YandexGPT

| Вы (3 мин) | Агент |
|------------|-------|
| 1. [Yandex Cloud Console](https://console.cloud.yandex.ru/iam) → сервисный аккаунт | Создаёт `.env` |
| 2. Роль `ai.languageModels.user` | Запускает прокси на `localhost:8444` |
| 3. Создать API-ключ, запомнить Folder ID | Регистрирует провайдер |

**Модели:** YandexGPT Lite · YandexGPT · YandexGPT 32K

### Яндекс 360 (Диск, Календарь, Почта, Телемост)

| Вы (3 мин) | Агент |
|------------|-------|
| 1. OAuth-приложение на [oauth.yandex.ru/client/new](https://oauth.yandex.ru/client/new) | Запускает `yax auth device` |
| 2. Redirect URI: `https://oauth.yandex.ru/verification_code` | Показывает URL + код для подтверждения |
| 3. Скоупы: `cloud_api:disk.*`, `calendar:all`, `mail:*`, `telemost-api:*` | |

Все четыре сервиса используют один OAuth-токен.

### Yandex Cloud

| Вы (2 мин) | Агент |
|------------|-------|
| 1. Установить `yc`: `curl -sSL https://storage.yandexcloud.net/yandexcloud-yc/install.sh \| bash` | Работает с `yc` напрямую |
| 2. `yc init` → следовать инструкциям | Storage, Compute, DNS, Serverless, БД и 30+ сервисов |

### Яндекс Метрика

| Вы (3 мин) | Агент |
|------------|-------|
| 1. OAuth-приложение на [oauth.yandex.com](https://oauth.yandex.com/?dialog=create-client-entry) | Вызывает Reporting API |
| 2. Скоупы: `metrika:read`, `metrika:write` | Управляет счётчиками и целями |
| 3. Получить токен и сохранить в `.env.local` как `YM_OAUTH_TOKEN` | Настраивает трекинг в Next.js/React |

---

## Архитектура

```
                           AI-агент

  "GigaChat"       "YandexGPT"      "Yandex Cloud"     "Метрика"
       │                 │                 │                 │
       ▼                 ▼                 ▼                 ▼
 ┌───────────┐   ┌────────────┐   ┌────────────────┐  ┌─────────────┐
 │ gpt2giga  │   │ Node.js    │   │ yc CLI         │  │ Metrika API │
 │ :8443     │   │ :8444      │   │                │  │ (REST)      │
 └─────┬─────┘   └─────┬──────┘   └──┬────┬────┬───┘  └──────┬──────┘
       │               │             │    │    │              │
       ▼               ▼             ▼    ▼    ▼              ▼
   Sber API     Foundation      Storage DNS  VMs    api-metrika.
   (OAuth)      Models API      Compute ...  ...    yandex.net
```

---

## Безопасность

- Креды хранятся локально с ограниченными правами
- Прокси слушают только `127.0.0.1`
- GigaChat-токены обновляются автоматически (~30 мин)
- Яндекс 360 — OAuth device code flow, пароли не сохраняются
- В файлах скиллов нет захардкоженных токенов и секретов

---

## Решение проблем

| Симптом | Причина | Решение |
|---------|---------|--------|
| GigaChat `401` | Токен истёк | Перезапустить `start-proxy.sh` |
| GigaChat `402` | Исчерпана квота | Переключить модель: Max → Pro → Lite |
| YandexGPT `403` | Неверный Folder ID | Проверить `.env` |
| `yc: command not found` | CLI не в PATH | `export PATH="$HOME/yandex-cloud/bin:$PATH"` |
| `yc` не авторизован | Нет токена | `yc init` |
| Метрика `403` | Нет скоупа | Переавторизоваться с `metrika:read` |
| Метрика — нет данных | Счётчик не работает | Проверить `code_status`, убрать `ssr:true` для статик-экспорта |
| Яндекс 360 — токен истёк | OAuth refresh | `yax auth device` |
| Почта — таймаут IMAP/SMTP | Порты заблокированы | Использовать VPS или локальную машину |

---

## Документация

Подробная документация в `SKILL.md` каждого скилла:

- [GigaChat](./gigachat/SKILL.md) — управление токенами, агенты, модели
- [YandexGPT](./yandexgpt/SKILL.md) — прокси, маппинг моделей
- [Яндекс 360](./yax/SKILL.md) — Disk API, CalDAV, Mail, Telemost, OAuth
- [Yandex Cloud](./yandex-cloud/SKILL.md) — полный справочник `yc` CLI, 30+ сервисов
- [Яндекс Метрика](./yandex-metrika/SKILL.md) — Management, Reporting & Logs API, настройка в Next.js

---

## Ссылки

- [GigaChat API](https://developers.sber.ru/docs/ru/gigachat/overview)
- [gpt2giga](https://pypi.org/project/gpt2giga/)
- [YandexGPT API](https://cloud.yandex.ru/docs/foundation-models/)
- [Yandex Cloud CLI](https://cloud.yandex.ru/docs/cli/)
- [Яндекс Метрика API](https://yandex.ru/dev/metrika/)
- [Яндекс OAuth](https://oauth.yandex.ru/)

---

Создал [@smvlx](https://github.com/smvlx)
