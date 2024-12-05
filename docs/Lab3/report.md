## Отчет (Report)

### Получение отчета за квартал

Позволяет получить отчет о проживаниях и доходах за указанный квартал.

- **URL:** `/report/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Параметры запроса:

- `quarter` (обязательный): Номер квартала (1-4).

### Пример запроса:

```
GET /report/?quarter=3 HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
{
    "stays_per_room": [
        {
            "room__room_id": 1,
            "client_count": 5
        },
        ...
    ],
    "rooms_per_floor": [
        {
            "floor": 1,
            "room_count": 10
        },
        ...
    ],
    "income_per_room": [
        {
            "room_id": 1,
            "total_income": 5000.0
        },
        ...
    ],
    "total_income": 20000.0
}
```
