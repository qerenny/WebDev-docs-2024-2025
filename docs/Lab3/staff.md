## Сотрудники (Staff)

### Получение списка сотрудников

Позволяет получить список всех сотрудников.

- **URL:** `/staff/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

**Параметры запроса:**

- `search` (необязательный): Поиск по фамилии или имени.

**Пример запроса:**

```
GET /staff/?search=Сидоров HTTP/1.1
Authorization: Token your_auth_token
```

**Успешный ответ:**

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

### Добавление нового сотрудника

Позволяет добавить нового сотрудника.

- **URL:** `/staff/`
- **Method:** `POST`
- **Auth required:** `NO`
- **Permission required:** `None`

**Тело запроса:**

```json
{
    "last_name": "Новиков",
    "first_name": "Николай",
    "middle_name": "Николаевич"
}
```

**Успешный ответ:**

```json
{
    "staff_id": 2,
    "last_name": "Новиков",
    "first_name": "Николай",
    "middle_name": "Николаевич"
}
```

### Получение информации о сотруднике

Позволяет получить информацию о конкретном сотруднике по его ID.

- **URL:** `/staff/{staff_id}/`
- **Method:** `GET`
- **Auth required:** `NO`
- **Permission required:** `None`

*Пример запроса:*

```
GET /staff/1/ HTTP/1.1
Authorization: Token your_auth_token
```

*Успешный ответ:*

```json
{
    "staff_id": 1,
    "last_name": "Сидоров",
    "first_name": "Сидор",
    "middle_name": "Сидорович"
}
```

### Обновление информации о сотруднике

Позволяет обновить информацию о сотруднике.

- **URL:** `/staff/{staff_id}/`
- **Method:** `PUT` или `PATCH`
- **Auth required:** `NO`
- **Permission required:** `None`

*Тело запроса:*

```json
{
    "middle_name": "Иванович"
}
```

*Успешный ответ:*

```json
{
    "staff_id": 1,
    "last_name": "Сидоров",
    "first_name": "Сидор",
    "middle_name": "Иванович"
}
```

### Удаление сотрудника

Позволяет удалить сотрудника.

- **URL:** `/staff/{staff_id}/`
- **Method:** `DELETE`
- **Auth required:** `NO`
- **Permission required:** `None`

*Успешный ответ:*

```
HTTP 204 No Content
```
