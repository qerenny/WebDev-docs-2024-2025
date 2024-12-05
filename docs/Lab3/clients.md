## Клиенты (Clients)

### Получение списка клиентов

Позволяет получить список всех клиентов.

- **URL:** `/clients/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Параметры запроса:**

- `city_of_origin` (необязательный): Фильтр по городу прибытия.
- `search` (необязательный): Поиск по фамилии, имени или номеру паспорта.

**Пример запроса:**

```
GET /clients/?search=Иванов HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```json
[
    {
        "client_id": 1,
        "user": {
            "id": 2,
            "username": "ivanov",
            "email": "ivanov@example.com"
        },
        "passport_number": "1234567890",
        "last_name": "Иванов",
        "first_name": "Иван",
        "middle_name": "Иванович",
        "city_of_origin": "Москва"
    },
    ...
]
```

### Регистрация нового клиента

Позволяет зарегистрировать нового клиента.

- **URL:** `/clients/`
- **Method:** `POST`
- **Auth required:** `YES`
- **Permission required:** `None`

**Тело запроса:**

```json
{
    "passport_number": "0987654321",
    "last_name": "Петров",
    "first_name": "Петр",
    "middle_name": "Петрович",
    "city_of_origin": "Санкт-Петербург"
}
```

**Успешный ответ:**

```json
{
    "client_id": 2,
    "user": {
        "id": 3,
        "username": "current_user",
        "email": "user@example.com"
    },
    "passport_number": "0987654321",
    "last_name": "Петров",
    "first_name": "Петр",
    "middle_name": "Петрович",
    "city_of_origin": "Санкт-Петербург"
}
```

### Получение информации о клиенте

Позволяет получить информацию о конкретном клиенте по его ID.

- **URL:** `/clients/{client_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Пример запроса:**

```
GET /clients/1/ HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```json
{
    "client_id": 1,
    "user": {
        "id": 2,
        "username": "ivanov",
        "email": "ivanov@example.com"
    },
    "passport_number": "1234567890",
    "last_name": "Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "city_of_origin": "Москва"
}
```

### Обновление информации о клиенте

Позволяет обновить информацию о клиенте.

- **URL:** `/clients/{client_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `YES`
- **Permission required:** `None`

**Тело запроса:**

```json
{
    "city_of_origin": "Новосибирск"
}
```

**Успешный ответ:**

```json
{
    "client_id": 1,
    "user": {
        "id": 2,
        "username": "ivanov",
        "email": "ivanov@example.com"
    },
    "passport_number": "1234567890",
    "last_name": "Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "city_of_origin": "Новосибирск"
}
```

### Удаление клиента

Позволяет удалить клиента.

- **URL:** `/clients/{client_id}/`
- **Method:** `DELETE`
- **Auth required:** `YES`
- **Permission required:** `None`

**Успешный ответ:**

```
HTTP 204 No Content
```

### Получение количества клиентов из города

Позволяет получить количество клиентов из определенного города.

- **URL:** `/clients/count_by_city/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Параметры запроса:**

- `city` (обязательный): Название города.

**Пример запроса:**

```
GET /clients/count_by_city/?city=Москва HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```json
{
    "city": "Москва",
    "client_count": 5
}
```

### Поиск клиентов, проживавших одновременно

Позволяет получить список клиентов, которые проживали в гостинице одновременно с указанным клиентом.

- **URL:** `/clients/clients_shared_stay/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Параметры запроса:**

- `client_id` (обязательный): ID клиента.

**Пример запроса:**

```
GET /clients/clients_shared_stay/?client_id=1 HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```json
[
    {
        "client_id": 2,
        "user": {
            "id": 3,
            "username": "petrov",
            "email": "petrov@example.com"
        },
        "passport_number": "0987654321",
        "last_name": "Петров",
        "first_name": "Петр",
        "middle_name": "Петрович",
        "city_of_origin": "Санкт-Петербург"
    },
    ...
]
```
