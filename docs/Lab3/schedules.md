## Расписание уборок (Cleaning Schedules)

### Получение списка расписаний уборок

Позволяет получить список всех расписаний уборок.

- **URL:** `/cleaning-schedules/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Параметры запроса:

- `staff__staff_id` (необязательный): Фильтр по ID сотрудника.
- `floor` (необязательный): Фильтр по этажу.
- `time_and_day_of_week` (необязательный): Фильтр по времени и дню недели.
- `search` (необязательный): Поиск по фамилии сотрудника или времени уборки.

### Пример запроса:

```
GET /cleaning-schedules/?floor=2 HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
[
    {
        "cleaning_schedule_id": 1,
        "staff": { ... },
        "room": null,
        "floor": 2,
        "time_and_day_of_week": "Понедельник 10:00"
    },
    ...
]
```

### Добавление нового расписания уборки

Позволяет добавить новое расписание уборки.

- **URL:** `/cleaning-schedules/`
- **Method:** `POST`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "staff_id": 1,
    "floor": 3,
    "time_and_day_of_week": "Вторник 14:00"
}
```

### Успешный ответ:

```json
{
    "cleaning_schedule_id": 2,
    "staff": { ... },
    "room": null,
    "floor": 3,
    "time_and_day_of_week": "Вторник 14:00"
}
```

### Получение информации о расписании уборки

Позволяет получить информацию о конкретном расписании уборки по его ID.

- **URL:** `/cleaning-schedules/{cleaning_schedule_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Пример запроса:

```
GET /cleaning-schedules/1/ HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
{
    "cleaning_schedule_id": 1,
    "staff": { ... },
    "room": null,
    "floor": 2,
    "time_and_day_of_week": "Понедельник 10:00"
}
```

### Обновление информации о расписании уборки

Позволяет обновить информацию о расписании уборки.

- **URL:** `/cleaning-schedules/{cleaning_schedule_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "time_and_day_of_week": "Среда 16:00"
}
```

### Успешный ответ:

```json
{
    "cleaning_schedule_id": 1,
    "staff": { ... },
    "room": null,
    "floor": 2,
    "time_and_day_of_week": "Среда 16:00"
}
```

### Удаление расписания уборки

Позволяет удалить расписание уборки.

- **URL:** `/cleaning-schedules/{cleaning_schedule_id}/`
- **Method:** `DELETE`
- **Auth required:** `NO`
- **Permission required:** `None`

### Успешный ответ:

```
HTTP 204 No Content
```