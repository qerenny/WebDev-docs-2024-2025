## Номера (Rooms)

### Получение списка номеров

Позволяет получить список всех номеров.

- **URL:** `/rooms/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Параметры запроса:**

- `room_type` (необязательный): Фильтр по типу номера (SINGLE, DOUBLE, TRIPLE).
- `floor` (необязательный): Фильтр по этажу.
- `search` (необязательный): Поиск по номеру телефона или ID номера.

**Пример запроса:**

```
GET /rooms/?room_type=SINGLE&floor=2 HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```
HTTP 200 OK
Content-Type: application/json

[
    {
        "room_id": 1,
        "room_type": "SINGLE",
        "cost_per_day": "100.00",
        "phone_number": "+123456789",
        "floor": 2,
        "is_available": true
    },
    ...
]
```

### Добавление нового номера

Позволяет добавить новый номер.

- **URL:** `/rooms/`
- **Method:** `POST`
- **Auth required:** `NO`
- **Permission required:** `None`

**Тело запроса:**

```json
{
    "room_type": "DOUBLE",
    "cost_per_day": "150.00",
    "phone_number": "+987654321",
    "floor": 3,
    "is_available": true
}
```

**Успешный ответ:**

```
HTTP 201 Created
Content-Type: application/json

{
    "room_id": 5,
    "room_type": "DOUBLE",
    "cost_per_day": "150.00",
    "phone_number": "+987654321",
    "floor": 3,
    "is_available": true
}
```

### Получение информации о номере

Позволяет получить информацию о конкретном номере по его ID.

- **URL:** `/rooms/{room_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Пример запроса:**

```
GET /rooms/1/ HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

```json
{
    "room_id": 1,
    "room_type": "SINGLE",
    "cost_per_day": "100.00",
    "phone_number": "+123456789",
    "floor": 2,
    "is_available": true
}
```

### Обновление информации о номере

Позволяет обновить информацию о номере.

- **URL:** `/rooms/{room_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `NO`
- **Permission required:** `None`

**Тело запроса:**

```json
{
    "cost_per_day": "120.00"
}
```

**Успешный ответ:**

```json
{
    "room_id": 1,
    "room_type": "SINGLE",
    "cost_per_day": "120.00",
    "phone_number": "+123456789",
    "floor": 2,
    "is_available": true
}
```

### Удаление номера

Позволяет удалить номер.

- **URL:** `/rooms/{room_id}/`
- **Method:** `DELETE`
- **Auth required:** `NO`
- **Permission required:** `None`

**Успешный ответ:**

```
HTTP 204 No Content
```

### Получение количества доступных номеров

Позволяет получить количество номеров, доступных для бронирования.

- **URL:** `/rooms/available_rooms_count/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Успешный ответ:**

```json
{
    "available_rooms": 10
}
```
