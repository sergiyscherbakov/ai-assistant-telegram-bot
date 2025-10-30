# 🤖 AI-АСИСТЕНТ TELEGRAM BOT

## Автор
**Розробник:** Сергій Щербаков
**Email:** sergiyscherbakov@ukr.net
**Telegram:** @s_help_2010

### 💰 Підтримати розробку
Задонатити на каву USDT (BINANCE SMART CHAIN):
**`0xDFD0A23d2FEd7c1ab8A0F9A4a1F8386832B6f95A`**

---

## 📋 СИСТЕМНІ ВИМОГИ
- N8N platform (self-hosted або cloud)
- Telegram Bot API токен
- OpenAI API ключ
- FAL AI API ключ
- Redis database (локальний або хмарний)

## 📁 СТРУКТУРА ПРОЕКТУ
```
AI_Assistant_[ПІБ]/
├── workflow.json          # N8N workflow експорт
├── README.md             # Ця інструкція
├── config/
│   ├── credentials.md    # Список необхідних credentials
│   └── settings.json     # Налаштування workflow
├── examples/
│   ├── generated_images/ # Приклади згенерованих зображень
│   ├── audio_samples/    # Приклади синтезу мовлення
│   └── transcriptions/   # Приклади транскрипції
└── screenshots/
    ├── workflow_overview.png
    ├── telegram_demo.png
    └── memory_system.png
```

## 🔑 НАЛАШТУВАННЯ CREDENTIALS У N8N

### 1. TELEGRAM BOT TOKEN
1. Перейти до **Settings** → **Credentials** 
2. Натиснути **"Add Credential"**
3. Обрати **"Telegram"**
4. Заповнити поля:
   ```
   Name: Telegram Bot Account
   Access Token: 1234567890:ABC***defGHI***xyz123
   ```
5. Натиснути **"Save"**

### 2. OPENAI API KEY
1. У меню Credentials натиснути **"Add Credential"**
2. Обрати **"OpenAI"**
3. Заповнити:
   ```
   Name: OpenAI API Account
   API Key: sk-proj-abc123***def456***ghi789
   ```
4. Натиснути **"Save"**

### 3. REDIS CONNECTION
1. Додати новий credential → **"Redis"**
2. Налаштувати підключення:
   ```
   Name: Redis Memory Store
   Host: redis-*****.upstash.io
   Port: 6379
   Password: abc123***xyz789
   Database: 0
   ```
3. Натиснути **"Test Connection"** → **"Save"**

### 4. FAL AI HTTP AUTH
1. Створити credential → **"HTTP Header Auth"**
2. Налаштувати авторизацію:
   ```
   Name: FAL AI Authorization
   Header Name: Authorization
   Header Value: Key fal_abc123***def456***xyz789
   ```
3. Натиснути **"Save"**

## ⚙️ ІМПОРТ ТА НАЛАШТУВАННЯ WORKFLOW

### Крок 1: Імпорт workflow
1. У N8N натиснути **"Import from file"**
2. Обрати файл `workflow.json`
3. Натиснути **"Import"**
4. Workflow з'явиться в редакторі

### Крок 2: Призначення credentials
Для кожної ноди з credential полем:

| Нода | Credential Field | Обрати |
|------|------------------|---------|
| Telegram Trigger | telegramApi | Telegram Bot Account |
| OpenAI Chat Model | openAiApi | OpenAI API Account |
| Redis Chat Memory | redis | Redis Memory Store |
| Generate audio | openAiApi | OpenAI API Account |
| Transcribe recording | openAiApi | OpenAI API Account |
| Analyze image | openAiApi | OpenAI API Account |
| Create an Image | httpHeaderAuth | FAL AI Authorization |
| Check Status | httpHeaderAuth | FAL AI Authorization |
| Get the image | httpHeaderAuth | FAL AI Authorization |

**Для кожної ноди:**
1. Клікнути на ноду
2. У панелі справа знайти поле **"Credential"**
3. Клікнути dropdown і обрати відповідний credential
4. Натиснути **"Save"** (Ctrl+S)

### Крок 3: Оновлення Chat ID
У всіх Telegram Send нодах замінити `chatId`:
1. Відкрити ноду **"Send a photo message1"**
2. Змінити поле `chatId` на свій ID:
   ```json
   "chatId": "-100********123"
   ```
3. Повторити для всіх Send нод
4. Натиснути **"Save"**

## 🚀 ЗАПУСК ТА ТЕСТУВАННЯ

### Активація workflow
1. Натиснути **"Active"** toggle у правому верхньому куті
2. Статус має змінитися на **"Active"**
3. Webhook URL буде згенеровано автоматично

### Тестування команд
Надіслати у Telegram bot:

**1. Генерація зображень:**
```
/draw
космічний кіт у скафандрі
```

**2. Транскрипція аудіо:**
```
/voice2text
[надіслати голосове повідомлення]
```

**3. Синтез мовлення:**
```
/text2speech
Привіт, це тестове повідомлення для синтезу мовлення
```

**4. Розпізнавання тексту:**
```
/ocr
[надіслати зображення з текстом]
```

## 🔧 НАЛАШТУВАННЯ HTTP REQUEST НОД

### FAL AI Authentication
Для всіх FAL AI нод (`Create an Image`, `Check Status`, `Get the image`):

1. Відкрити ноду
2. У секції **"Authentication"**:
   - Authentication: **Generic Credential Type**
   - Generic Auth Type: **HTTP Header Auth**
   - Credential: **FAL AI Authorization**
3. У секції **"Headers"** додати:
   ```json
   {
     "name": "Content-Type",
     "value": "application/json"
   }
   ```
4. Натиснути **"Save"**

### Telegram File API
Для ноди `HTTP Request1` (OCR):
- URL: `https://api.telegram.org/bot{BOT_TOKEN}/getFile`
- Method: **GET**
- Authentication: **None**
- Query Parameters: `file_id={{$json.file_id}}`

## 📊 МОНІТОРИНГ ТА ЛОГИ

### Перевірка Redis
1. Відкрити ноду **"Get the value of a key from Redis"**
2. Натиснути **"Test step"**
3. Перевірити чи повертаються збережені команди

### Дебаг workflow
1. Увімкнути **"Save Executions"** у настройках
2. Переглядати логи в **"Executions"** таб
3. Перевіряти **JSON** вихід кожної ноди

## ⚠️ ПОШИРЕНІ ПОМИЛКИ

| Помилка | Причина | Рішення |
|---------|---------|---------|
| 401 Unauthorized | Неправильний API ключ | Перевірити credential налаштування |
| 404 Not Found | Неправильний URL | Перевірити endpoints у HTTP нодах |
| Redis Connection Error | Неправильні дані підключення | Протестувати Redis credential |
| Telegram Webhook Failed | Неправильний bot token | Перевірити Telegram credential |
| FAL AI Timeout | Перевантаження сервісу | Збільшити час очікування у Wait ноді |

## 💰 ВАРТІСТЬ ЕКСПЛУАТАЦІЇ

**Місячні витрати (1000 запитів/день):**
- OpenAI GPT-4o-mini: ~$58.50
- FAL AI Flux: ~$15.00
- OpenAI TTS: ~$45.00
- Whisper API: ~$1.20
- Redis hosting: ~$10.00
- **Загалом: ~$129.70/місяць**

## 📞 ПІДТРИМКА

При виникненні проблем:
1. Перевірити статус всіх credentials
2. Переглянути execution logs
3. Протестувати кожну ноду окремо
4. Перевірити баланс API аккаунтів