### Проект YaMDb

**YaMDb** - сервис для сбора отзывов на произведения.

Проект YaMDb собирает отзывы (**Review**) пользователей на произведения (**Titles**). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий (**Category**) может быть расширен администратором.
Благодарные или возмущённые читатели оставляют к произведениям текстовые отзывы (**Review**) и выставляют произведению рейтинг. Из пользовательских оценок формируется усреднённая оценка произведения — рейтинг. На одно произведение пользователь может оставить только один отзыв.

### Технологии

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white)
![DjangoREST](https://img.shields.io/badge/DJANGO-REST-ff1709?style=for-the-badge&logo=django&logoColor=white&color=ff1709&labelColor=gray)
![JWT](https://img.shields.io/badge/JWT-black?style=for-the-badge&logo=JSON%20web%20tokens)

### Инструкция по запуску проекта для Windows:

- Склонируйте проект из репозитория:

```sh
$ git clone https://github.com/dmitrykokh/api_yamdb.git
```

- Установите и активируйте виртуальное окружение:

```sh
python -m venv venv 
source venv/Scripts/activate
``` 

- Установите зависимости из файла requirements.txt:

```sh
pip install -r requirements.txt
``` 

- Выполните миграции в папке с файлом manage.py:

```sh
python manage.py migrate
```

- Загрузите тестовые данные. В папке с файлом manage.py выполните команду:

```sh
python python manage.py importcsv --path static/data/users.csv --mod <имя модели> --app reviews
```

Модели нужно указывать в следующем порядке:

```sh
User
Category
Genre
Title
Genre
Review
Comment
```

Пример запуска команды для импорта модели User:

```sh
python python manage.py importcsv --path static/data/users.csv --mod User --app reviews
```

- Запустите dev-сервер. В папке с файлом manage.py выполните команду:

```sh
python manage.py runserver
```

***

# Ресурсы API YaMDb

**AUTH**: аутентификация.

**TITLES**: произведения, к которым пишут отзывы (определённый фильм, книга или песенка).

**CATEGORIES**: категории (типы) произведений ("Фильмы", "Книги", "Музыка").

**GENRES**: жанры произведений. Одно произведение может быть привязано к нескольким жанрам.

**REVIEWS**: отзывы на произведения. Отзыв привязан к определённому произведению.

**COMMENTS**: комментарии к отзывам. Комментарий привязан к определённому отзыву.

# Пользовательские роли

**Аноним** — может просматривать описания произведений, читать отзывы и комментарии.

**Аутентифицированный пользователь (user)** — может читать всё, как и Аноним, дополнительно может публиковать отзывы и ставить рейтинг произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы и ставить им оценки; может редактировать и удалять свои отзывы и комментарии.

**Модератор (moderator)** — те же права, что и у Аутентифицированного пользователя плюс право удалять и редактировать любые отзывы и комментарии.

**Администратор (admin)** — полные права на управление проектом и всем его содержимым. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.

**Администратор Django** — те же права, что и у роли Администратор.

### Алгоритм регистрации пользователей

Для добавления нового пользователя нужно отправить POST-запрос на эндпоинт:

```
POST /api/v1/auth/signup/
```

- В запросе необходимо передать поля:

1. ```username``` - имя пользователя;
2. ```email``` - почта пользователя.

Сервис YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email.

Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт:

```
POST /api/v1/auth/token/
```

- В запросе необходимо передать поля:

1. ```username``` - имя пользователя;
2. ```confirmation_code``` - код подтверждения.
 
В ответ на запрос пользователю придет ```token``` (JWT-токен).
В результате пользователь получает токен и может работать с API проекта, отправляя этот токен с каждым запросом.

После регистрации и получения токена пользователь может заполнить поля в своём профайле путем отправки запроса на эндпоинт:

```
PATCH /api/v1/users/me/
```

### Регистрация пользователей админом

Пользователя может создать администратор — через админ-зону сайта или через запрос на специальный эндпоинт:

```
POST /api/v1/users/
```

- Письмо с кодом отправлять не нужно.

После этого пользователь должен самостоятельно отправить POST-запросы как при самостоятельной регистрации.

### Примеры использования API для неавторизованных пользователей:

Для неавторизованных пользователей работа с API доступна в режиме чтения.

```sh
При указании параметров 'limit' и 'offset' выдача работает с пагинацией.
GET api/v1/categories/ - получить список всех категорий.
GET /api/v1/genres/ - получить список всех жанров.
GET /api/v1/titles/ - получить список всех произведений.
GET /api/titles/{title_id}/reviews/ - получение списка отзывов по id произведения.
GET /api/titles/{title_id}/reviews/{review_id}/ - получение отзыва по id для указанного произведения.
GET /api/titles/{title_id}/reviews/{review_id}/comments/ - получение списка всех комментариев к отзыву
GET /api/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/ - получение комментария по id для указанного отзыва.
```

Подробная документация к API с доступными методами находится по адресу ```http://127.0.0.1:8000/redoc/```

## Запуск проекта:

### Для запуска проекта, применения миграций, создания суперюзера, загрузки статики и добавления в БД данных из фикстур соответственно необходимо в папке infra выполнить команды:

```sh
docker-compose up -d --build
sudo docker-compose exec web python manage.py migrate
sudo docker-compose exec web python manage.py createsuperuser
sudo docker-compose exec web python manage.py collectstatic --no-input
sudo docker-compose exec web python manage.py loaddata fixtures.json
```

После запуска контейнера админка доступна по адресу:
```sh
/admin/
```
для остановки контейнера необходимо в папке infra выполнить:
```sh
 docker-compose down -v
```

***

### Автор

Дмитрий Кох