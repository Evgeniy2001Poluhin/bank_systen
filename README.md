# FinanceGolang - Банковское приложение

FinanceGolang - это бэкэнд банковского приложения, разработанный на Go, с функциями управления счетами, кредитами и транзакциями. Включает в себя REST API и JWT аутентификацию для безопасного взаимодействия.

---

## ✨ Возможности

* **Управление счетами:** Создание, просмотр, пополнение, снятие и переводы.
* **Кредиты:** Оформление, отслеживание платежей и просмотр графиков.
* **История транзакций:** Детальная информация по всем операциям.
* **Аутентификация:** Безопасный доступ через JWT токены.
* **REST API:** Чистый и интуитивно понятный интерфейс для интеграции.

---

## 🛠️ Технологический стек

* **Backend:** Go 1.21+ (основной язык), Gin Web Framework, GORM (ORM).
* **База данных:** PostgreSQL (реляционная БД).
* **Безопасность:** JWT (аутентификация и авторизация), хеширование паролей.
* **Контейнеризация:** Docker, Docker Compose.
* **Тестирование:** Python (тестовый клиент).

---

## 🚀 Запуск приложения

Вы можете запустить приложение несколькими способами. **Рекомендуемый способ - с использованием Docker Compose.**

### 1. Запуск в Docker (рекомендуется)

Используйте Docker Compose для полного развертывания приложения и базы данных.

1.  **Клонируйте репозиторий:**
    ```bash
    git clone [https://github.com/Mishazx/FinanceGolang.git](https://github.com/Mishazx/FinanceGolang.git)
    cd FinanceGolang
    ```
2.  **Запустите:**
    ```bash
    docker compose up --build
    ```
    Приложение будет доступно по адресу `http://localhost:8080`.

    * **Примечание:** Возможно, потребуется создать файлы `public_key.asc` и `private_key.asc` в директории `FinanceGolang/keys/` для JWT-авторизации, если их нет. Пример генерации:
        ```bash
        mkdir -p FinanceGolang/keys
        cd FinanceGolang/keys
        ssh-keygen -t rsa -b 4096 -m PEM -f private_key.asc -N ""
        ssh-keygen -e -f private_key.asc -m PEM > public_key.asc
        ```
        Убедитесь, что ваш `Dockerfile` или `docker-compose.yml` копирует эти ключи в `/app/` внутри контейнера.

### 2. Локальный запуск с PostgreSQL в Docker

Этот метод запускает только базу данных в Docker, а само Go-приложение - локально.

1.  **Установите Go-зависимости:**
    ```bash
    go mod download
    ```
2.  **Запустите только PostgreSQL:**
    ```bash
    docker compose up -d postgres
    ```
3.  **Создайте файл `.env`** в корневой директории проекта (`FinanceGolang/`) со следующим содержимым:
    ```ini
    # Настройки сервера
    SERVER_HOST=localhost
    SERVER_PORT=8080

    # База данных
    DB_TYPE=postgres
    DB_HOST=localhost
    DB_PORT=5432
    DB_USER=postgres
    DB_PASSWORD=postgres
    DB_NAME=bank
    DB_SSLMODE=disable

    # JWT
    JWT_SECRET=your-secret-key # Замените на надежный секретный ключ
    JWT_EXPIRATION=24 # Часы

    # Приложение
    APP_ENV=development
    APP_DEBUG=true
    ```
4.  **Запустите приложение Go:**
    ```bash
    go run src/main.go
    ```

### 3. Полностью локальный запуск

Предполагает, что у вас локально установлены Go и PostgreSQL.

1.  **Установите Go-зависимости:**
    ```bash
    go mod download
    ```
2.  **Убедитесь, что локальный PostgreSQL запущен** и настроен для работы с приложением (используйте файл `.env` из предыдущего пункта, изменив `DB_HOST` на `localhost` или соответствующий IP).
3.  **Запустите приложение Go:**
    ```bash
    go run src/main.go
    ```

---

## 🧪 Тестирование API

Для взаимодействия с API вы можете использовать Python-клиент или `curl`.

### Через Python-клиент

1.  **Перейдите в директорию клиента:**
    ```bash
    cd tests/client
    ```
2.  **Создайте и активируйте виртуальное окружение:**
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate  # Для Linux/macOS
    # или
    .venv\Scripts\activate     # Для Windows
    ```
3.  **Установите зависимости:**
    ```bash
    pip install -r requirements.txt
    ```
4.  **Запустите клиент:**
    ```bash
    python main.py
    ```

### Через curl

Примеры основных запросов:

* **Регистрация нового пользователя:**
    ```bash
    curl -X POST http://localhost:8080/api/auth/register \
      -H "Content-Type: application/json" \
      -d '{
        "email": "user@example.com",
        "password": "password123"
      }'
    ```

* **Вход и получение токена:**
    ```bash
    curl -X POST http://localhost:8080/api/auth/login \
      -H "Content-Type: application/json" \
      -d '{
        "email": "user@example.com",
        "password": "password123"
      }'
    ```

* **Использование полученного токена (пример запроса счетов):**
    ```bash
    # Сначала сохраните полученный токен из предыдущего ответа
    export TOKEN="ваш_полученный_токен"

    curl -X GET http://localhost:8080/api/accounts \
      -H "Authorization: Bearer $TOKEN"
    ```

---

## ⚙️ Конфигурация

Приложение настраивается через **переменные окружения** или файл `.env`.

| Переменная       | Описание                         | По умолчанию        |
| :--------------- | :------------------------------- | :------------------ |
| `SERVER_HOST`    | Хост сервера                     | `localhost`         |
| `SERVER_PORT`    | Порт сервера                     | `8080`              |
| `DB_TYPE`        | Тип базы данных                  | `postgres`          |
| `DB_HOST`        | Хост базы данных                 | `localhost`         |
| `DB_PORT`        | Порт базы данных                 | `5432`              |
| `DB_USER`        | Пользователь БД                  | `postgres`          |
| `DB_PASSWORD`    | Пароль БД                        | `postgres`          |
| `DB_NAME`        | Имя базы данных                  | `bank`              |
| `DB_SSLMODE`     | Режим SSL для БД                 | `disable`           |
| `JWT_SECRET`     | Секретный ключ JWT               | `your-secret-key`   |
| `JWT_EXPIRATION` | Время жизни токена (в часах)     | `24`                |
| `APP_ENV`        | Окружение (`development`/`production`) | `development`       |
| `APP_DEBUG`      | Режим отладки                    | `true`              |

---

## 📝 API Endpoints

Все защищенные эндпоинты требуют валидный JWT-токен в заголовке `Authorization: Bearer <token>`.

### Аутентификация

* `POST /api/auth/register` - Регистрация нового пользователя.
* `POST /api/auth/login` - Вход пользователя и получение JWT-токена.
* `POST /api/auth/refresh` - Обновление JWT-токена.

### Счета

* `GET /api/accounts` - Получить список счетов текущего пользователя.
* `POST /api/accounts` - Создать новый счет.
* `GET /api/accounts/:id` - Получить информацию о конкретном счете.
* `GET /api/accounts/:id/transactions` - Получить историю транзакций для счета.
* `POST /api/accounts/:id/deposit` - Пополнить счет.
* `POST /api/accounts/:id/withdraw` - Снять средства со счета.
* `POST /api/accounts/:id/transfer` - Осуществить перевод между счетами.

### Кредиты

* `POST /api/credits` - Оформить новый кредит.
* `GET /api/credits` - Получить список кредитов текущего пользователя.
* `GET /api/credits/:id` - Получить информацию о конкретном кредите.
* `GET /api/credits/:id/schedule` - Получить график платежей по кредиту.
* `POST /api/credits/:id/payment` - Внести платеж по кредиту.

### Транзакции

* `GET /api/transactions` - Получить историю транзакций текущего пользователя.
* `GET /api/transactions/:id` - Получить детали конкретной транзакции.
* `POST /api/transactions` - Создать новую транзакцию.

### Карты

* `POST /api/cards` - Создать новую карту.
* `GET /api/cards` - Получить все карты текущего пользователя.

### Аналитика

* `POST /api/analytics` - Получить аналитические данные.
* `GET /api/analytics/accounts/:id/forecast` - Получить прогноз баланса для счета.

---

## 🔒 Безопасность

* Все API-эндпоинты (кроме регистрации и входа) защищены JWT аутентификацией.
* Пароли пользователей хешируются с использованием bcrypt.
* Поддержка SSL для подключения к базе данных.
* Валидация входящих данных для предотвращения некорректных запросов.

---

## 📁 Структура проекта
.
├── src/
│   ├── config/            # Конфигурация приложения
│   ├── controller/        # HTTP контроллеры
│   ├── database/          # Конфигурация и миграции БД
│   ├── dto/               # Data Transfer Objects
│   ├── model/             # Модели данных
│   ├── repository/        # Слой доступа к данным
│   ├── security/          # Аутентификация и авторизация
│   └── service/           # Бизнес-логика
├── tests/
│   ├── client/            # Тестовый клиент на Python
│   └── test/              # Интеграционные тесты
├── .template.env          # Env шаблон
├── docker-compose.yml     # Docker конфигурация
├── Dockerfile            # Сборка приложения
└── README.md             # Документация
