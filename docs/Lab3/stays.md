## Проживания (Stays)

### Получение списка проживаний

Позволяет получить список всех проживаний.

- **URL:** `/stays/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Параметры запроса:

- `client__client_id` (необязательный): Фильтр по ID клиента.
- `room__room_id` (необязательный): Фильтр по ID номера.
- `check_in_date` (необязательный): Фильтр по дате заезда.
- `check_out_date` (необязательный): Фильтр по дате выезда.
- `search` (необязательный): Поиск по фамилии клиента или ID номера.

### Пример запроса:

```
GET /stays/?client__client_id=1 HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
[
    {
        "stay_id": 1,
        "client": { ... },
        "room": { ... },
        "check_in_date": "2023-10-01",
        "check_out_date": "2023-10-10"
    },
    ...
]
```

### Добавление нового проживания

Позволяет добавить новое проживание.

- **URL:** `/stays/`
- **Method:** `POST`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "client_id": 1,
    "room_id": 2,
    "check_in_date": "2023-10-15",
    "check_out_date": "2023-10-20"
}
```

### Успешный ответ:

```json
{
    "stay_id": 3,
    "client": { ... },
    "room": { ... },
    "check_in_date": "2023-10-15",
    "check_out_date": "2023-10-20"
}
```

_Примечание: При добавлении проживания номер автоматически помечается как занятый (`is_available: false`)._

### Получение информации о проживании

Позволяет получить информацию о конкретном проживании по его ID.

- **URL:** `/stays/{stay_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Пример запроса:

```
GET /stays/1/ HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
{
    "stay_id": 1,
    "client": { ... },
    "room": { ... },
    "check_in_date": "2023-10-01",
    "check_out_date": "2023-10-10"
}
```

### Обновление информации о проживании

Позволяет обновить информацию о проживании.

- **URL:** `/stays/{stay_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "check_out_date": "2023-10-12"
}
```

### Успешный ответ:

```json
{
    "stay_id": 1,
    "client": { ... },
    "room": { ... },
    "check_in_date": "2023-10-01",
    "check_out_date": "2023-10-12"
}
```

_Примечание: При обновлении проживания статус номера обновляется в зависимости от даты выезда._

### Удаление проживания

Позволяет удалить проживание.

- **URL:** `/stays/{stay_id}/`
- **Method:** `DELETE`
- **Auth required:** `NO`
- **Permission required:** `None`

### Успешный ответ:

```
HTTP 204 No Content
```

_Примечание: При удалении проживания номер автоматически помечается как свободный (`is_available: true`)._

### Получение проживаний в номере за период

Позволяет получить список проживаний в указанном номере за определенный период.

- **URL:** `/stays/stays_in_room/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Параметры запроса:

- `room_id` (обязательный): ID номера.
- `start_date` (обязательный): Начало периода (YYYY-MM-DD).
- `end_date` (обязательный): Конец периода (YYYY-MM-DD).

### Пример запроса:

```
GET /stays/stays_in_room/?room_id=2&start_date=2023-10-01&end_date=2023-10-31 HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
[
    {
        "stay_id": 3,
        "client": { ... },
        "room": { ... },
        "check_in_date": "2023-10-15",
        "check_out_date": "2023-10-20"
    },
    ...
]
```
