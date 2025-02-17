# Лабораторная работа 6-2

## Дедлайн сдачи работы без пенальти

???

## Как выполнять

- Создать ветку `mod-2` из ветки `mod-1`

- Открыть ветку `mod-2` в workspace

## 0. Подготовка проекта

### 1. Создайте папку `.devcontainer` рядом с `note-app`

В этой папке создайте файл `devcontainer.json`

```json
{
    "image": "mcr.microsoft.com/devcontainers/universal:2",
    "features": {
        "ghcr.io/devcontainers/features/php:1": {
            "version": "8.3",
            "installComposer": true
        }
    }
}
```

### 2. Создайте в папке `note-app` файл `.env`

```env
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:uGipfSRgiLAI1Kf7vc93xwknsw8RIwdQtHslGLI9h0g=
APP_DEBUG=true
APP_TIMEZONE=UTC
APP_URL=http://localhost

APP_LOCALE=en
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=en_US

APP_MAINTENANCE_DRIVER=file
# APP_MAINTENANCE_STORE=database

PHP_CLI_SERVER_WORKERS=4

BCRYPT_ROUNDS=12

LOG_CHANNEL=stack
LOG_STACK=single
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=

SESSION_DRIVER=database
SESSION_LIFETIME=120
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_DOMAIN=null

BROADCAST_CONNECTION=log

FILESYSTEM_DISK=local
QUEUE_CONNECTION=database

CACHE_STORE=database

CACHE_PREFIX=

MEMCACHED_HOST=127.0.0.1


REDIS_CLIENT=phpredis
REDIS_HOST=127.0.0.1

REDIS_PASSWORD=null
REDIS_PORT=6379


MAIL_MAILER=log

MAIL_SCHEME=null
MAIL_HOST=127.0.0.1
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=

AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false


VITE_APP_NAME="${APP_NAME}"
```

_После этого сделайте коммит_

### 3. Удалите codespace и создайте его заного, чтобы подтянуть новый image

### 4. В файле `.gitignore` удалите строчку .env

### 5. Установите зависимости с помощью composer

```bash
composer install
```

### 6. Проверьте, что расширения для работы с Php установлены

## 1. Работа с моделями

### 1. Создание модели данных

```bash
php artisan make:model Note -m
```

### 2. Обновите файл миграций

Измените содержимое функции метода `create` в только что созданном файле в папке `database/migrations`

```php
public function up(): void
{
    Schema::create('notes', function (Blueprint $table) {
        $table->id();
        $table->longText('note');

        $table->foreignId('user_id')->constrained('users');
        $table->timestamps();
    });
}
```

### 3. Примените миграции

```bash
php artisan migrate
```

### 4. Создайте factory для работы с моделью Note

```bash
php artisan make:factory NoteFactory --model=Note
```

### 5. Измените функцию `definition`, чтобы она возвращала новый экземляр заметки

`database/factories/NoteFactory.php`

```php
public function definition(): array
{
    return [
        'note' => fake()->realText(2000),
        'user_id' => 1

    ];
}
```

### 6. Добавьте пользователю ID и пароль при создании пользователя

`database/seeders/DatabaseSeeder.php`

```php
class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        // User::factory(10)->create();

        User::factory()->create([
            // Пока что хардкодим айдишку
            'id' => 1,
            'name' => 'Test User',
            'email' => 'test@example.com',
            // и хардкодим пароль
            'password' => bcrypt('pass123.')
        ]);

        // Создаем 100 заметок для теста
        Note::factory(100)->create();
    }
}
```

### 7. Добавьте factory к модели Note

`app/Models/Note.php`

```php
class Note extends Model
{
    use HasFactory;

    protected $fillable = ['note', 'user_id'];
}
```

### 7. Внесите изменения и запустите сид процесс на базе данных модели Note

```bash
php artisan migrate:refresh --seed
```

> [!NOTE]
>
> **Задание 1**
>
> Создайте модель для задач и мигрируйте её
>
> **Todo**
> |Поле|Тип данных|
> |-|-|
> |name|string(с ограничением в 75 символов)|
> |done|boolean|
> |urgent|boolean|
> |dateCompleted|timedate|
>
> Для реализации данного функционала придется обратиться к [документации по типам в Laravel](https://laravel.com/docs/11.x/migrations)

## 2.
