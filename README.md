# Солодкий Агент 009 🍰🤖

![Version](https://img.shields.io/badge/status-demo-purple)
![AI](https://img.shields.io/badge/AI-ChatGPT_|_Claude-blue)
![Stack](https://img.shields.io/badge/stack-n8n_|_Telegram_|_Google-green)

### Опис

**Солодкий Агент 009** — це AI-менеджер для кондитера-одинака, який приймає замовлення замість людини. 

Бот працює у Telegram 24/7: вітається з клієнтом, дізнається про алергії, рекомендує торти з меню, перевіряє вільні дати, зберігає замовлення в Google Sheets CRM та автоматично генерує комерційну пропозицію в Google Docs — усе без участі власника.

**Живий прототип:** [@sweet_agent_009_bot](https://t.me/sweet_agent_009_bot) — напишіть "Привіт" і побачите AI в дії.

---

## Проблема

Кондитер-одинак витрачає **3-5 годин щодня** на переписки з клієнтами в Instagram та Telegram. Через повільні відповіді втрачає замовлення. Наймає адміністратора за **15-25 тис. грн/міс**, який працює лише 8 годин на день і теж не встигає.

## Рішення

AI-агент, який **повністю замінює адміністратора**:

| Клієнт отримує | Власник отримує |
|---|---|
| Миттєву відповідь у Telegram | Вільний час замість переписок |
| Меню з цінами та рекомендації | Всі замовлення в Google Sheets |
| Готову КП за 30 секунд | Ранковий звіт о 9:00 |
| Персональний підхід | Сповіщення про нестачу продуктів |

**Ціна:** 600 грн/міс vs 20 000 грн/міс за адміністратора.

---

## Функціонал

| Команда / Подія | Що робить AI |
|---|---|
| `Привіт` / `Хочу торт` | Починає діалог, запитує алергії, показує меню |
| Вибір торта | Рекомендує, перевіряє завантаженість (макс 3 замовлення) |
| Підтвердження замовлення | Зберігає в Google Sheets, списує склад |
| Генерація КП | Автоматично створює Google Docs з даними клієнта |
| 09:00 щодня | Відправляє власнику звіт за попередній день |

---

## Технологічний стек

| Компонент | Технологія |
|---|---|
| **AI-агент** | OpenRouter (ChatGPT-4o / Claude 3.5) |
| **Оркестрація** | n8n Cloud (13 workflow) |
| **Інтерфейс** | Telegram Bot API |
| **CRM** | Google Sheets (5 аркушів: клієнти, замовлення, меню, склад, звіти) |
| **Документи** | Google Docs (шаблон КП) |
| **Розсилка** | n8n Schedule Trigger |

---

## Як це працює (користувацький флоу)

```
Клієнт → Telegram → AI-агент (OpenRouter) → n8n → Google Sheets / Google Docs
```

**Детально:**
1. Клієнт пише боту "Хочу торт!"
2. AI вітається, дізнається про алергії (tool: `tool_get_client` + `tool_save_client`)
3. Показує меню з цінами з Google Sheets (tool: `tool_get_cake_types`)
4. Клієнт обирає торт і дату — AI перевіряє завантаженість (tool: `tool_check_capacity`)
5. Замовлення збережено! (tool: `tool_create_order` + списання складу)
6. КП згенеровано в Google Docs (tool: `tool_generate_kp`) — посилання в чаті

---

## Чому AI — ядро, а не прикраса

Без AI цей продукт не має сенсу. Саме AI:
- **Аналізує** природномовний запит клієнта
- **Приймає рішення** який інструмент викликати (з 9 доступних)
- **Рекомендує** торт на основі вподобань
- **Підтримує** контекст діалогу
- **Генерує** персоналізовану КП

Людина не потрібна в жодному кроці — це **Zero-Shot Customer Journey**.

---

## Структура репозиторію

```
📦 sweet-agent-009-workflows/
├── 📄 README.md               # Цей файл
├── 📄 Солодкий Агент 009.json # Головний AI-агент (OpenRouter)
├── 📄 agent_client.json       # Діалог з клієнтом
├── 📄 agent_owner.json        # Звіт власнику
├── 📄 error_handler.json      # Обробка помилок
├── 📄 schedule_morning_reminder.json # Ранковий звіт о 9:00
├── 📄 tool_check_capacity.json       # Перевірка завантаженості
├── 📄 tool_create_order.json         # Створення замовлення
├── 📄 tool_generate_kp.json          # Генерація КП в Google Docs
├── 📄 tool_get_cake_types.json       # Меню з Google Sheets
├── 📄 tool_get_client.json           # Отримання даних клієнта
├── 📄 tool_get_inventory.json        # Стан складу
├── 📄 tool_get_orders_by_date.json   # Замовлення на дату
├── 📄 tool_save_client.json          # Збереження клієнта
├── 📄 tool_update_inventory.json     # Оновлення складу
└── 📄 tool_update_order_status.json  # Статус замовлення
```

---

## Інструкція для запуску

> **Важливо:** Прототип уже задеплоєний. Ця інструкція — для локального запуску або fork-у.

### Вимоги

- Акаунт [n8n Cloud](https://app.n8n.cloud) (або self-hosted n8n)
- [OpenRouter API Key](https://openrouter.ai/keys)
- [Google Service Account](https://console.cloud.google.com/) з доступом до Sheets & Docs API
- [Telegram Bot Token](https://t.me/botfather)

### Встановлення

1. **Клонуйте репозиторій:**
   ```bash
   git clone https://github.com/SvetlanaRomaniuk111/sweet-agent-009-workflows.git
   cd sweet-agent-009-workflows
   ```

2. **Імпортуйте workflow в n8n:**
   - Відкрийте n8n → Workflows → Import from File
   - Виберіть `Солодкий Агент 009.json` — імпортується головний агент
   - Повторіть для всіх `tool_*.json` — вони підтягнуться автоматично

3. **Налаштуйте змінні середовища в n8n:**
   - `OPENROUTER_API_KEY` — ваш ключ OpenRouter
   - `GOOGLE_SERVICE_ACCOUNT_EMAIL` — email сервісного акаунта
   - `GOOGLE_PRIVATE_KEY` — приватний ключ
   - `SPREADSHEET_ID` — ID вашої Google Sheets таблиці (шаблон: 5 аркушів)
   - `TELEGRAM_BOT_TOKEN` — токен вашого бота

4. **Активуйте workflow:**
   - Telegram Trigger → Active
   - Schedule Trigger (ранковий звіт) → Active

---

## Артефакти для хакатону

| Артефакт | Статус | Посилання |
|---|---|---|
| ✅ GitHub-репозиторій | Готово | [sweet-agent-009-workflows](https://github.com/SvetlanaRomaniuk111/sweet-agent-009-workflows) |
| ✅ Задеплоєний прототип | Готово | [@sweet_agent_009_bot](https://t.me/sweet_agent_009_bot) |
| 🎥 Відеодемо | Зробити | Скринкаст 3-7 хв |
| 📊 Pitch-презентація | Готово | [Файл у workspace] |

---

## Команда

_Склад команди уточнюється_

| Ім'я | Роль |
|------|------|
| Артур Косовец | Розробник |
| Світлана Романюк | AI Creator (презентація, фото, GitHub, README) |

---

## Наступні кроки

- [ ] Підключення онлайн-оплати (LiqPay / Fondy)
- [ ] Інтеграція з Nova Poshta для відстеження доставки
- [ ] Додавання фото-галереї робіт кондитера
- [ ] Система відгуків після замовлення

---

*Хакатон "AI в Український Бізнес" 2026 · n8n + OpenRouter + Telegram*
