## Выполнение уборок (Cleaning Executions)

### Получение списка выполненных уборок

Позволяет получить список всех выполненных уборок.

- **URL:** `/cleaning-executions/`
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
GET /cleaning-executions/?staff__staff_id=1 HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
[
    {
        "cleaning_execution_id": 1,
        "cleaning_schedule": { ... },
        "staff": { ... },
        "time_and_day_of_week": "Понедельник 10:00",
        "floor": 2,
        "room": { ... }
    },
    ...
]
```

### Добавление нового выполнения уборки

Позволяет добавить запись о выполненной уборке.

- **URL:** `/cleaning-executions/`
- **Method:** `POST`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "cleaning_schedule_id": 1,
    "staff_id": 1,
    "time_and_day_of_week": "Понедельник 10:00",
    "floor": 2,
    "room_id": 1
}
```

### Успешный ответ:

```json
{
    "cleaning_execution_id": 2,
    "cleaning_schedule": { ... },
    "staff": { ... },
    "time_and_day_of_week": "Понедельник 10:00",
    "floor": 2,
    "room": { ... }
}
```

_Примечание: Если указаны `room_id` и `floor`, этаж номера должен совпадать с указанным этажом. В противном случае будет возвращена ошибка валидации._

### Получение информации о выполненной уборке

Позволяет получить информацию о конкретной выполненной уборке по ее ID.

- **URL:** `/cleaning-executions/{cleaning_execution_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Пример запроса:

```
GET /cleaning-executions/1/ HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
{
    "cleaning_execution_id": 1,
    "cleaning_schedule": { ... },
    "staff": { ... },
    "time_and_day_of_week": "Понедельник 10:00",
    "floor": 2,
    "room": { ... }
}
```

### Обновление информации о выполненной уборке

Позволяет обновить информацию о выполненной уборке.

- **URL:** `/cleaning-executions/{cleaning_execution_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `NO`
- **Permission required:** `None`

### Тело запроса:

```json
{
    "floor": 3
}
```

### Успешный ответ:

```json
{
    "cleaning_execution_id": 1,
    "cleaning_schedule": { ... },
    "staff": { ... },
    "time_and_day_of_week": "Понедельник 10:00",
    "floor": 3,
    "room": { ... }
}
```

### Удаление записи о выполненной уборке

Позволяет удалить запись о выполненной уборке.

- **URL:** `/cleaning-executions/{cleaning_execution_id}/`
- **Method:** `DELETE`
- **Auth required:** `NO`
- **Permission required:** `None`

### Успешный ответ:

```
HTTP 204 No Content
```

### Получение сотрудников, убирающих номер клиента

Позволяет получить список сотрудников, которые убирали номер клиента в указанный день недели.

- **URL:** `/cleaning-executions/staff_cleaned_client_room/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

### Параметры запроса:

- `client_id` (обязательный): ID клиента.
- `day_of_week` (обязательный): День недели (например, “Понедельник”).

### Пример запроса:

```
GET /cleaning-executions/staff_cleaned_client_room/?client_id=1&day_of_week=Понедельник HTTP/1.1
Authorization: Token your_auth_token
```

### Успешный ответ:

```json
[
    {
        "staff_id": 1,
        "last_name": "Сидоров",
        "first_name": "Сидор",
        "middle_name": "Сидорович"
    },
    ...
]
```
