# Effective Mobile Test

REST API сервис для обогащения сырых данных и занесения их в базу данных на основе PostgreSQL.

Данный сервис разработан, как тестовое задание для компании `Effective Mobile`. Он включает в себя:

## Мигратор базы данных  (`cmd/migrations/main.go`)

Позволяет с помощью одной команды мигрировать структуру базы данных и сами данные для дальнейшего использования в проекте.

Команда для мигрирования данных: `go run ./cmd/migrations/main.go --dbcl="postgres://dblogin:dbpass@localhost:5432/dbname?sslmode=disable" --mp="./migrations"`

***--dbcl (Database connection link)*** - url для подключения к базе данных. Необходимо предворительно создать базу данных, куда будут портироваться структуры и данные.

***--mp (Migrations path)*** - Путь до папок с файлами миграций.

<br>

## REST сервис (`cmd/server/main.go`)

Сервис, который получает ФИО из открытых API, обогащает
ответ наиболее вероятными возрастом, полом и национальностью и сохраняет данные в БД. По запросу выдает информацию о найденных людях.

Команда для запуска сервиса: `go run ./cmd/server/main.go`

Серевис реализован при помощи библиотеки *Chi*. Выбор основан на простоте роутинга и, что в любой момент написанные обработчики можно перевести на стандартную бибилиотеку.

### Роуты:
- *GET*: `http://127.0.0.1:8080/users/?params...` - GET запрос, в котором реализована фильтрация и пагинизация пользователей.

    Параметры:
    1) `offset=` - смещения для пагинизации;
    2) `limit=` - сколько записей может быть в одном запросе;
    3) `fname=` - фильтрация по имени;
    4) `sname=` - фильтрация по фамилии;
    5) `pname=` - фильтрация по отчеству;
    6) `age=` - фильтрация по возросту.

    *Пример:*
    ![Alt text](images/get.png)

- *POST*: `http://127.0.0.1:8080/users/save` - POST запрос, который сохраняет данные в БД.

    Входная структура:
    ```json
        {       
            "name": "Dmitriy",
            "surname": "Ushakov",
            "patronymic": "Vasilevich" // необязательно
        }
    ```
    *Пример:*
    ![Alt text](images/post.png)

- *DELETE*: `http://127.0.0.1:8080/users/delete?user_id=` - DELETE запрос, который удаляет пользователя из БД по ID пользователя.

    *Пример:*
    ![Alt text](images/delete.png)

- *PUT*: `http://127.0.0.1:8080/users/update` - PUT запрос для обновления данных в БД.

    Входная структура: 
    ```json
   {
    "user_id": 2,
    "fname": "Adam",
    "sname": "Jonson",
    "gender_name": "male",
    "age": 55,
    "region_code": "CZ"
    }
    ```

    *Пример:*
    ![Alt text](images/put.png)