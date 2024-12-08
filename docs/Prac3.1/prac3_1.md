# Практическая работа №3.1y.

### 1. Практическое задание 1

**Цель:**
Создать 6-7 новых объектов автовладельцев, 5-6 автомобилей, для каждого автовладельца добавить водительское удостоверение и привязать от 1 до 3 автомобилей. Использовать методы .save() и .add().

**Результаты:**
Для выполнения задания был реализован следующий код:

```python
from car_owners.models import CarOwner, Car, DriverLicense, Ownership
from datetime import datetime

# Создание владельцев
owner1 = CarOwner.objects.create(first_name="Иван", last_name="Иванов", birth_date=datetime(1980, 5, 17))
owner2 = CarOwner.objects.create(first_name="Петр", last_name="Петров", birth_date=datetime(1975, 8, 23))
owner3 = CarOwner.objects.create(first_name="Сергей", last_name="Сергеев", birth_date=datetime(1990, 12, 5))
owner4 = CarOwner.objects.create(first_name="Алексей", last_name="Алексеев", birth_date=datetime(1985, 3, 14))
owner5 = CarOwner.objects.create(first_name="Михаил", last_name="Михайлов", birth_date=datetime(1995, 7, 29))
owner6 = CarOwner.objects.create(first_name="Дмитрий", last_name="Дмитриев", birth_date=datetime(1988, 9, 9))
owner7 = CarOwner.objects.create(first_name="Николай", last_name="Николаев", birth_date=datetime(1992, 11, 11))

# Создание автомобилей
car1 = Car.objects.create(license_plate="A123BC77", brand="Toyota", model="Camry", color="Черный")
car2 = Car.objects.create(license_plate="B456CD99", brand="Honda", model="Civic", color="Белый")
car3 = Car.objects.create(license_plate="C789DE88", brand="Ford", model="Focus", color="Синий")
car4 = Car.objects.create(license_plate="D012EF66", brand="BMW", model="X5", color="Серый")
car5 = Car.objects.create(license_plate="E345FG55", brand="Audi", model="A6", color="Красный")
car6 = Car.objects.create(license_plate="F678GH44", brand="Mercedes", model="E200", color="Зеленый")

# Создание водительских удостоверений
license1 = DriverLicense.objects.create(
    car_owner=owner1,
    license_number="770001",
    license_type="B",
    issue_date=datetime(2000, 6, 15)
)
license2 = DriverLicense.objects.create(
    car_owner=owner2,
    license_number="770002",
    license_type="B",
    issue_date=datetime(1998, 9, 20)
)
license3 = DriverLicense.objects.create(
    car_owner=owner3,
    license_number="770003",
    license_type="B",
    issue_date=datetime(2010, 1, 5)
)
license4 = DriverLicense.objects.create(
    car_owner=owner4,
    license_number="770004",
    license_type="B",
    issue_date=datetime(2005, 4, 25)
)
license5 = DriverLicense.objects.create(
    car_owner=owner5,
    license_number="770005",
    license_type="B",
    issue_date=datetime(2015, 7, 30)
)
license6 = DriverLicense.objects.create(
    car_owner=owner6,
    license_number="770006",
    license_type="B",
    issue_date=datetime(2008, 11, 12)
)
license7 = DriverLicense.objects.create(
    car_owner=owner7,
    license_number="770007",
    license_type="B",
    issue_date=datetime(2012, 3, 18)
)

# Создание записей владения автомобилями
Ownership.objects.create(
    car_owner=owner1,
    car=car1,
    start_date=datetime(2020, 1, 1),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner1,
    car=car2,
    start_date=datetime(2021, 5, 10),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner2,
    car=car2,
    start_date=datetime(2019, 3, 15),
    end_date=datetime(2021, 5, 9)
)
Ownership.objects.create(
    car_owner=owner2,
    car=car3,
    start_date=datetime(2020, 7, 22),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner3,
    car=car4,
    start_date=datetime(2018, 12, 1),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner4,
    car=car5,
    start_date=datetime(2017, 6, 18),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner4,
    car=car6,
    start_date=datetime(2019, 8, 25),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner4,
    car=car1,
    start_date=datetime(2016, 2, 10),
    end_date=datetime(2020, 1, 1)
)
Ownership.objects.create(
    car_owner=owner5,
    car=car3,
    start_date=datetime(2021, 9, 5),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner6,
    car=car6,
    start_date=datetime(2020, 10, 30),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner7,
    car=car5,
    start_date=datetime(2022, 2, 14),
    end_date=None
)
Ownership.objects.create(
    car_owner=owner7,
    car=car4,
    start_date=datetime(2023, 4, 20),
    end_date=None
)
```

#### Отображение информации о владельце

```python
owners = CarOwner.objects.all()
for owner in owners:
    print(f"Владелец: {owner.first_name} {owner.last_name}, Дата рождения: {owner.birth_date.date()}")
    # Удостоверение
    try:
        license = DriverLicense.objects.get(car_owner=owner)
        print(f"  Удостоверение №{license.license_number}, Категория: {license.license_type}, Дата выдачи: {license.issue_date.date()}")
    except DriverLicense.DoesNotExist:
        print("  Удостоверение не найдено.")
    # Автомобили
    ownerships = Ownership.objects.filter(car_owner=owner)
    if ownerships.exists():
        print("  Автомобили:")
        for ownership in ownerships:
            car = ownership.car
            print(f"    {car.brand} {car.model}, Номер: {car.license_plate}, Цвет: {car.color}")
    else:
        print("  Автомобили не найдены.")
    print("-" * 40)
```
**Результаты:**
Владелец: poka ola, Дата рождения: 2024-10-02
  Удостоверение не найдено.
  Автомобили:
    misubatsi m34, Номер: 1234, Цвет: red
    playstation 6, Номер: 00000, Цвет: purple
    volkswolf 0000291, Номер: 101010, Цвет: #123412
----------------------------------------
Владелец: chao hola, Дата рождения: 1899-10-10
  Удостоверение не найдено.
  Автомобили:
    playstation 6, Номер: 00000, Цвет: purple
    volkswolf 0000291, Номер: 101010, Цвет: #123412
    changan qr2, Номер: 756392, Цвет: gray
----------------------------------------
Владелец: Иван Иванов, Дата рождения: 1980-05-17
  Удостоверение №770001, Категория: B, Дата выдачи: 2000-06-15
  Автомобили:
    Toyota Camry, Номер: A123BC77, Цвет: Черный
    Honda Civic, Номер: B456CD99, Цвет: Белый
----------------------------------------
Владелец: Петр Петров, Дата рождения: 1975-08-23
  Удостоверение №770002, Категория: B, Дата выдачи: 1998-09-20
  Автомобили:
    Honda Civic, Номер: B456CD99, Цвет: Белый
    Ford Focus, Номер: C789DE88, Цвет: Синий
----------------------------------------
Владелец: Сергей Сергеев, Дата рождения: 1990-12-05
  Удостоверение №770003, Категория: B, Дата выдачи: 2010-01-05
  Автомобили:
    BMW X5, Номер: D012EF66, Цвет: Серый
----------------------------------------
Владелец: Алексей Алексеев, Дата рождения: 1985-03-14
  Удостоверение №770004, Категория: B, Дата выдачи: 2005-04-25
  Автомобили:
    Audi A6, Номер: E345FG55, Цвет: Красный
    Mercedes E200, Номер: F678GH44, Цвет: Зеленый
    Toyota Camry, Номер: A123BC77, Цвет: Черный
----------------------------------------
Владелец: Михаил Михайлов, Дата рождения: 1995-07-29
  Удостоверение №770005, Категория: B, Дата выдачи: 2015-07-30
  Автомобили:
    Ford Focus, Номер: C789DE88, Цвет: Синий
----------------------------------------
Владелец: Дмитрий Дмитриев, Дата рождения: 1988-09-09
  Удостоверение №770006, Категория: B, Дата выдачи: 2008-11-12
  Автомобили:
    Mercedes E200, Номер: F678GH44, Цвет: Зеленый
----------------------------------------
Владелец: Николай Николаев, Дата рождения: 1992-11-11
  Удостоверение №770007, Категория: B, Дата выдачи: 2012-03-18
  Автомобили:
    Audi A6, Номер: E345FG55, Цвет: Красный
    BMW X5, Номер: D012EF66, Цвет: Серый
----------------------------------------

### 2. Практическое задание 2

**Цель:**
Реализовать запросы на выборку данных, используя различные методы Django ORM.

**Результаты:**
#### 1. Все машины марки “Toyota”:

```python
toyota_cars = Car.objects.filter(brand="Toyota")
for car in toyota_cars:
    print(car)
```

**Результат:**
Toyota Camry (A123BC77).

#### 2. Все водители с именем “Иван”:

```python
owners_named_ivan = CarOwner.objects.filter(first_name="Иван")
for owner in owners_named_ivan:
    print(owner)
```

**Результат:**
Иван Иванов.

#### 3. ID случайного владельца и его водительское удостоверение:

```python
owner = CarOwner.objects.order_by('?').first()
owner_id = owner.id
driver_license = DriverLicense.objects.get(car_owner__id=owner_id)
print(driver_license)
```

**Результат:**
Удостоверение №770001 (Иван Иванов).

#### 4. Владельцы красных машин:

```python
owners_with_red_cars = CarOwner.objects.filter(
    ownerships__car__color="Красный"
).distinct()
for owner in owners_with_red_cars:
    print(owner)
```

**Результат:**
Алексей Алексеев, Николай Николаев.

#### 5. Владельцы, начавшие владеть машиной с 2020 года:

```python
owners_since_2020 = CarOwner.objects.filter(
    ownerships__start_date__year=2020
).distinct()
for owner in owners_since_2020:
    print(owner)
```

**Результат:**
Иван Иванов, Петр Петров, Дмитрий Дмитриев.

### 3. Практическое задание 3

**Цель:**
Демонстрация работы с агрегатами, аннотациями, группировками и сортировками.

**Результаты:**
#### 1. Дата самого раннего удостоверения:

```python
oldest_license_date = DriverLicense.objects.aggregate(oldest_issue_date=Min('issue_date'))
print(f"Дата выдачи самого старшего удостоверения: {oldest_license_date['oldest_issue_date'].date()}")
```

**Результат:**
Дата выдачи самого старшего удостоверения: 1998-09-20

#### 2. Самая поздняя дата владения машиной:

```python
latest_ownership_date = Ownership.objects.aggregate(latest_start_date=Max('start_date'))
print(f"Самая поздняя дата начала владения машиной: {latest_ownership_date['latest_start_date'].date()}")
```

**Результат:**
Самая поздняя дата начала владения машиной: 2024-10-19

#### 3. Количество машин у каждого владельца:

```python
owners_car_count = CarOwner.objects.annotate(car_count=Count('ownerships__car', distinct=True))
for owner in owners_car_count:
    print(f"{owner.first_name} {owner.last_name} владеет {owner.car_count} машинами")
```

**Результат:**
poka ola владеет 3 машинами
chao hola владеет 3 машинами
Иван Иванов владеет 2 машинами
Петр Петров владеет 2 машинами
Сергей Сергеев владеет 1 машинами
Алексей Алексеев владеет 3 машинами
Михаил Михайлов владеет 1 машинами
Дмитрий Дмитриев владеет 1 машинами
Николай Николаев владеет 2 машинами

#### 4. Количество машин каждой марки:

```python
brand_car_count = Car.objects.values('brand').annotate(count=Count('id'))
for item in brand_car_count:
    print(f"Марка {item['brand']} имеет {item['count']} машин")
```

**Результат:**
Марка Audi имеет 1 машин
Марка BMW имеет 1 машин
Марка Ford имеет 1 машин
Марка Honda имеет 1 машин
Марка Mercedes имеет 1 машин
Марка Toyota имеет 1 машин
Марка changan имеет 1 машин
Марка misubatsi имеет 1 машин
Марка playstation имеет 1 машин
Марка volkswolf имеет 1 машин

#### 5. Сортировка владельцев по дате выдачи удостоверения:

```python
owners_with_license = CarOwner.objects.filter(driver_license__isnull=False).distinct().order_by('driver_license__issue_date')
for owner in owners_with_license:
    license_issue_date = owner.driver_license.first().issue_date.date()
    print(f"{owner.first_name} {owner.last_name}, дата выдачи удостоверения: {license_issue_date}")
```

**Результат:**
Петр Петров, дата выдачи удостоверения: 1998-09-20
Иван Иванов, дата выдачи удостоверения: 2000-06-15
Алексей Алексеев, дата выдачи удостоверения: 2005-04-25
Дмитрий Дмитриев, дата выдачи удостоверения: 2008-11-12
Сергей Сергеев, дата выдачи удостоверения: 2010-01-05
Николай Николаев, дата выдачи удостоверения: 2012-03-18
Михаил Михайлов, дата выдачи удостоверения: 2015-07-30