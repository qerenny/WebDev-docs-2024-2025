# Документация проекта: Система управления гостиницей

Проект реализует REST API для управления данными о гостиничных номерах, клиентах, сотрудниках, проживаниях, расписаниях уборок и записях о выполнении уборок. Также предусмотрена возможность формирования отчетов.

## Содержание
1. Структура данных (Модели)
    - [Room](#room)
    - [Client](#сlient)
    - [Staff](#staff)
    - [Stay](#stay)
    - [CleaningSchedule](#cleaningschedule)
    - [CleaningExecution](#cleaningexecution)
2. Сериализаторы
    - [UserSerializer](#userserializer)
    - [RoomSerializer](#roomserializer)
    - [ClientSerializer](#clientserializer)
    - [StaffSerializer](#staffserializer)
    - [StaySerializer](#stayserializer)
    - [CleaningScheduleSerializer](#cleaningscheduleserializer)
    - [CleaningExecutionSerializer](#cleaningexecutionserializer)
3. Вьюсеты и логика API
    - [RoomViewSet](#roomviewset)
    - [ClientViewSet](#clientviewset)
    - [StaffViewSet](#staffviewset)
    - [StayViewSet](#stayviewset)
    - [CleaningScheduleViewSet](#cleaningscheduleviewset)
    - [CleaningExecutionViewSet](#cleaningexecutionviewset)
    - [ReportView](#reportview)
4. [Маршрутизация (URLs)](#routes)

## Модели

### Room

Представляет данные о номере гостиницы, включая тип (одноместный, двухместный, трехместный), стоимость за день, контактный телефон, этаж и статус доступности.

```python
class Room(models.Model):
    SINGLE = 'SINGLE'
    DOUBLE = 'DOUBLE'
    TRIPLE = 'TRIPLE'

    ROOM_TYPE_CHOICES = [
        (SINGLE, 'Одноместный'),
        (DOUBLE, 'Двухместный'),
        (TRIPLE, 'Трехместный'),
    ]

    room_id = models.AutoField(primary_key=True)
    room_type = models.CharField(
        max_length=10,
        choices=ROOM_TYPE_CHOICES,
        verbose_name='Тип номера'
    )
    cost_per_day = models.DecimalField(
        max_digits=10,
        decimal_places=2,
        validators=[MinValueValidator(0)],
        verbose_name='Стоимость за день'
    )
    phone_number = models.CharField(
        max_length=15,
        verbose_name='Номер телефона'
    )
    floor = models.PositiveIntegerField(verbose_name='Этаж')
    is_available = models.BooleanField(default=True, verbose_name='Свободен')

    def __str__(self):
        return f"Номер: {self.room_id} ({self.get_room_type_display()}, Этаж: {self.floor})"
```

### Client

Отражает данные о клиенте. Связана с моделью пользователя (User) один-к-одному и хранит паспортные данные, ФИО и город прибытия.

```python
class Client(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    client_id = models.AutoField(primary_key=True)
    passport_number = models.CharField(
        max_length=20,
        unique=True,
        verbose_name='Номер паспорта'
    )
    last_name = models.CharField(max_length=50, verbose_name='Фамилия')
    first_name = models.CharField(max_length=50, verbose_name='Имя')
    middle_name = models.CharField(
        max_length=50,
        null=True,
        blank=True,
        verbose_name='Отчество'
    )
    city_of_origin = models.CharField(
        max_length=100,
        verbose_name='Город прибытия'
    )

    def __str__(self):
        return f"{self.last_name} {self.first_name}, Паспорт: {self.passport_number}"
```

### Модель Staff

Хранит информацию о сотрудниках гостиницы.

```python
class Staff(models.Model):
    staff_id = models.AutoField(primary_key=True)
    last_name = models.CharField(max_length=50, verbose_name='Фамилия')
    first_name = models.CharField(max_length=50, verbose_name='Имя')
    middle_name = models.CharField(
        max_length=50,
        null=True,
        blank=True,
        verbose_name='Отчество'
    )

    def __str__(self):
        return f"{self.last_name} {self.first_name} {self.middle_name}"
```

### Stay

Отражает период проживания клиента в конкретном номере. Хранит данные о датах заезда и выезда.

```python
class Stay(models.Model):
    stay_id = models.AutoField(primary_key=True)
    client = models.ForeignKey(
        Client,
        on_delete=models.CASCADE,
        related_name='stays',
        verbose_name='Клиент'
    )
    room = models.ForeignKey(
        Room,
        on_delete=models.CASCADE,
        related_name='stays',
        verbose_name='Номер'
    )
    check_in_date = models.DateField(verbose_name='Дата заезда')
    check_out_date = models.DateField(verbose_name='Дата выезда')

    def __str__(self):
        return f"Проживание {self.client} в номере {self.room}"
```

### CleaningSchedule

Отражает расписание уборки номеров, включая сотрудника, номер (опционально), этаж и время-день недели.

```python
class CleaningSchedule(models.Model):
    cleaning_schedule_id = models.AutoField(primary_key=True)
    staff = models.ForeignKey(
        Staff,
        on_delete=models.CASCADE,
        related_name='cleaning_schedules',
        verbose_name='Сотрудник'
    )
    room = models.ForeignKey(
        Room,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name='cleaning_schedules',
        verbose_name='Номер'
    )
    floor = models.PositiveIntegerField(verbose_name='Этаж')
    time_and_day_of_week = models.CharField(
        max_length=50,
        verbose_name='Время и день недели'
    )

    def __str__(self):
        return f"Расписание уборки {self.staff} на этаже {self.floor}"
```

### CleaningExecution

Хранит данные о выполненных уборках, включая расписание, сотрудника, время, этаж и номер.

```python
class CleaningExecution(models.Model):
    cleaning_execution_id = models.AutoField(primary_key=True)
    cleaning_schedule = models.ForeignKey(
        CleaningSchedule,
        on_delete=models.CASCADE,
        related_name='executions',
        verbose_name='Расписание уборки'
    )
    staff = models.ForeignKey(
        Staff,
        on_delete=models.CASCADE,
        related_name='cleaning_executions',
        verbose_name='Сотрудник'
    )
    time_and_day_of_week = models.CharField(
        max_length=50,
        verbose_name='Время и день недели'
    )
    floor = models.PositiveIntegerField(verbose_name='Этаж')
    room = models.ForeignKey(
        Room,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name='cleaning_executions',
        verbose_name='Номер'
    )

    def __str__(self):
        return f"Уборка {self.staff} в {self.time_and_day_of_week}"
```

## Сериализаторы

Для преобразования данных моделей в JSON и обратно используются сериализаторы Django REST Framework:

```python
from rest_framework import serializers
from .models import Room, Client, Staff, Stay, CleaningSchedule, CleaningExecution
from django.contrib.auth import get_user_model

User = get_user_model()
```

### UserSerializer

Преобразует данные пользователя (из стандартной модели User).

```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email')
```

### RoomSerializer

Предоставляет полный набор полей модели Room.

```python
class RoomSerializer(serializers.ModelSerializer):
    class Meta:
        model = Room
        fields = '__all__'
```

### ClientSerializer

Отражает данные о клиенте, а также вложенный сериализатор UserSerializer для представления связанного пользователя. Позволяет создавать клиента только после аутентификации.

ClientSerializer при создании проверяет аутентификацию пользователя и привязывает профиль клиента к текущему пользователю. Таким образом, предотвращается дублирование профилей и обеспечивается связность данных.


```python
class ClientSerializer(serializers.ModelSerializer):
    user = UserSerializer(read_only=True)

    class Meta:
        model = Client
        fields = '__all__'

    def create(self, validated_data):
        user = self.context['request'].user
        if not user.is_authenticated:
            raise serializers.ValidationError("Пользователь должен быть аутентифицирован.")
        if Client.objects.filter(user=user).exists():
            raise serializers.ValidationError("Профиль клиента уже существует.")
        validated_data['user'] = user
        return super().create(validated_data)
```

### StaffSerializer

Простой сериализатор для модели Staff.

```python
class StaffSerializer(serializers.ModelSerializer):
    class Meta:
        model = Staff
        fields = '__all__'
```

### StaySerializer

Сериализатор проживания. Вложенно отображает данные клиента и номера, но при создании или обновлении использует client_id и room_id поля для передачи первичных ключей.

```python
class StaySerializer(serializers.ModelSerializer):
    client = ClientSerializer(read_only=True)
    room = RoomSerializer(read_only=True)
    client_id = serializers.PrimaryKeyRelatedField(
        queryset=Client.objects.all(), source='client', write_only=True
    )
    room_id = serializers.PrimaryKeyRelatedField(
        queryset=Room.objects.all(), source='room', write_only=True
    )

    class Meta:
        model = Stay
        fields = ['stay_id', 'client', 'room', 'client_id', 'room_id',
                  'check_in_date', 'check_out_date']
```

### CleaningScheduleSerializer

Использует вложенные сериализаторы для сотрудника и номера. При создании можно указать staff_id и room_id.

```python
class CleaningScheduleSerializer(serializers.ModelSerializer):
    staff = StaffSerializer(read_only=True)
    staff_id = serializers.PrimaryKeyRelatedField(
        queryset=Staff.objects.all(), source='staff', write_only=True
    )
    room = RoomSerializer(read_only=True)
    room_id = serializers.PrimaryKeyRelatedField(
        queryset=Room.objects.all(), source='room', write_only=True, allow_null=True, required=False
    )

    class Meta:
        model = CleaningSchedule
        fields = ['cleaning_schedule_id', 'staff', 'staff_id', 'room', 'room_id',
                  'floor', 'time_and_day_of_week']
```

### CleaningExecutionSerializer

Отражает факт выполнения уборки по расписанию. Валидация проверяет совпадение этажа с этажом номера.

В CleaningExecutionSerializer реализована валидация, проверяющая соответствие этажа номера (room.floor) этажу, указанному в самой записи о выполнении уборки (floor). Если данные не совпадают, сериализатор возвращает ошибку. Это гарантирует целостность данных и логику, при которой указанные в записи параметры уборки будут корректными и соответствующими реальной структуре помещения.


```python
class CleaningExecutionSerializer(serializers.ModelSerializer):
    cleaning_schedule = CleaningScheduleSerializer(read_only=True)
    cleaning_schedule_id = serializers.PrimaryKeyRelatedField(
        queryset=CleaningSchedule.objects.all(), source='cleaning_schedule', write_only=True
    )
    staff = StaffSerializer(read_only=True)
    staff_id = serializers.PrimaryKeyRelatedField(
        queryset=Staff.objects.all(), source='staff', write_only=True
    )
    room = RoomSerializer(read_only=True)
    room_id = serializers.PrimaryKeyRelatedField(
        queryset=Room.objects.all(), source='room', write_only=True, allow_null=True, required=False
    )

    class Meta:
        model = CleaningExecution
        fields = [
            'cleaning_execution_id', 'cleaning_schedule', 'cleaning_schedule_id',
            'staff', 'staff_id', 'time_and_day_of_week', 'floor', 'room', 'room_id'
        ]

    def validate(self, attrs):
        room = attrs.get('room')
        floor = attrs.get('floor')
        if room and floor and room.floor != floor:
            raise serializers.ValidationError({
                'floor': 'Указанный этаж не совпадает с этажом номера.'
            })
        return attrs
```

## Вьюсеты и API

Данные вьюсеты представляют REST API для соответствующих моделей, поддерживая стандартные операции CRUD, поиск, фильтрацию и дополнительные методы (через @action).

Используются DjangoFilterBackend и SearchFilter из Django REST Framework.
Например, для RoomViewSet можно фильтровать по room_type или floor, а искать по phone_number или room_id.

Аналогично для клиентов (ClientViewSet), сотрудников (StaffViewSet) и других моделей есть возможность оперативно находить записи по релевантным полям. Это упрощает интеграцию с фронтендом или другими системами, где важна гибкая фильтрация данных.

Во вьюсетах реализованы дополнительные действия:
**ClientViewSet:**
•	count_by_city: возвращает количество клиентов из указанного города.
•	clients_shared_stay: показывает список клиентов, проживавших одновременно.
**RoomViewSet:**
•	available_rooms_count: возвращает число доступных номеров.
**StayViewSet:**
•	stays_in_room: позволяет получить список проживаний в конкретном номере за заданный период.
**CleaningExecutionViewSet:**
•	staff_cleaned_client_room: определяет, какие сотрудники убирали номер клиента в определённый день недели.

### RoomViewSet

Управление номерами, поиск, фильтрация и метод available_rooms_count для подсчета доступных номеров.

```python
class RoomViewSet(viewsets.ModelViewSet):
    queryset = Room.objects.all()
    serializer_class = RoomSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['room_type', 'floor']
    search_fields = ['phone_number', 'room_id']

    @action(detail=False, methods=['get'])
    def available_rooms_count(self, request):
        count = Room.objects.filter(is_available=True).count()
        return Response({'available_rooms': count})
```

### ClientViewSet

CRUD для клиентов, поиск по ФИО, номеру паспорта и городу. Дополнительные методы: подсчет клиентов по городу и поиск клиентов, проживавших одновременно.

```python
class ClientViewSet(viewsets.ModelViewSet):
    queryset = Client.objects.all()
    serializer_class = ClientSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['city_of_origin']
    search_fields = ['last_name', 'first_name', 'passport_number']

    @action(detail=False, methods=['get'])
    def count_by_city(self, request):
        city = request.query_params.get('city')
        if not city:
            return Response({'error': 'Необходимо указать параметр city'}, status=400)
        count = Client.objects.filter(city_of_origin=city).count()
        return Response({'city': city, 'client_count': count})

    @action(detail=False, methods=['get'])
    def clients_shared_stay(self, request):
        client_id = request.query_params.get('client_id')
        if not client_id:
            return Response({'error': 'Необходимо указать client_id'}, status=400)

        try:
            client = Client.objects.get(client_id=client_id)
        except Client.DoesNotExist:
            return Response({'error': 'Клиент не найден'}, status=404)

        client_stays = Stay.objects.filter(client=client)
        overlapping_conditions = Q()
        for stay in client_stays:
            overlapping_conditions |= (Q(check_in_date__lte=stay.check_out_date) & Q(check_out_date__gte=stay.check_in_date))

        overlapping_stays = Stay.objects.filter(overlapping_conditions, ~Q(client=client)).select_related('client')
        other_clients = {stay.client for stay in overlapping_stays}
        serializer = ClientSerializer(other_clients, many=True)
        return Response(serializer.data)
```

### StaffViewSet

CRUD по сотрудникам с возможностью поиска по фамилии и имени.

```python
class StaffViewSet(viewsets.ModelViewSet):
    queryset = Staff.objects.all()
    serializer_class = StaffSerializer
    filter_backends = [filters.SearchFilter]
    search_fields = ['last_name', 'first_name']
```

### StayViewSet

CRUD для проживаний. Позволяет фильтровать по клиенту, номеру и датам. Имеет метод для получения проживаний в номере за период и методы perform_create, perform_update, perform_destroy для изменения статуса номера.

При создании (perform_create) нового Stay номер становится занятым (is_available = False).
При обновлении (perform_update) информация о выезде (check_out_date) проверяется: если дата выезда истекла (прошла текущую дату), номер освобождается (статус возвращается к is_available = True).
При удалении (perform_destroy) проживания номер автоматически становится доступным, поскольку отсутствуют данные, указывающие на текущее заселение.

Данная логика упрощает управление доступностью номеров, не требуя ручного обновления статуса после каждой операции.

```python
class StayViewSet(viewsets.ModelViewSet):
    queryset = Stay.objects.all()
    serializer_class = StaySerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['client__client_id', 'room__room_id', 'check_in_date', 'check_out_date']
    search_fields = ['client__last_name', 'room__room_id']

    @action(detail=False, methods=['get'])
    def stays_in_room(self, request):
        room_id = request.query_params.get('room_id')
        start_date = request.query_params.get('start_date')
        end_date = request.query_params.get('end_date')

        if not room_id or not start_date or not end_date:
            return Response({'error': 'Необходимо указать room_id, start_date и end_date'}, status=400)

        stays = Stay.objects.filter(
            room__room_id=room_id,
            check_in_date__lte=end_date,
            check_out_date__gte=start_date
        )

        serializer = self.get_serializer(stays, many=True)
        return Response(serializer.data)

    def perform_create(self, serializer):
        stay = serializer.save()
        stay.room.is_available = False
        stay.room.save()

    def perform_update(self, serializer):
        stay = serializer.save()
        if stay.check_out_date and stay.check_out_date <= date.today():
            stay.room.is_available = True
        else:
            stay.room.is_available = False
        stay.room.save()

    def perform_destroy(self, instance):
        room = instance.room
        instance.delete()
        room.is_available = True
        room.save()
```

### CleaningScheduleViewSet

CRUD и фильтрация расписаний уборки.

```python
class CleaningScheduleViewSet(viewsets.ModelViewSet):
    queryset = CleaningSchedule.objects.all()
    serializer_class = CleaningScheduleSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['staff__staff_id', 'floor', 'time_and_day_of_week']
    search_fields = ['staff__last_name', 'time_and_day_of_week']
```

### CleaningExecutionViewSet

CRUD и фильтрация записей о выполненных уборках. Дополнительный метод staff_cleaned_client_room для поиска сотрудников, убиравших номер клиента в конкретный день.

```python
class CleaningExecutionViewSet(viewsets.ModelViewSet):
    queryset = CleaningExecution.objects.all()
    serializer_class = CleaningExecutionSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['staff__staff_id', 'floor', 'time_and_day_of_week']
    search_fields = ['staff__last_name', 'time_and_day_of_week']

    @action(detail=False, methods=['get'])
    def staff_cleaned_client_room(self, request):
        client_id = request.query_params.get('client_id')
        day_of_week = request.query_params.get('day_of_week')

        if not client_id or not day_of_week:
            return Response({'error': 'Необходимо указать client_id и day_of_week'}, status=400)

        stay = Stay.objects.filter(client__client_id=client_id).last()
        if not stay:
            return Response({'error': 'Пребывание клиента не найдено'}, status=404)

        cleanings = CleaningExecution.objects.filter(
            room=stay.room,
            time_and_day_of_week__icontains=day_of_week
        ).select_related('staff')

        staff_members = [cleaning.staff for cleaning in cleanings]
        serializer = StaffSerializer(staff_members, many=True)
        return Response(serializer.data)
```

### ReportView

Генерация отчетов за указанный квартал с информацией о проживании, доходах и статистике по номерам и этажам.

Отдельное представление ReportView генерирует отчёты за указанный квартал. Логика определения дат квартала встроена во вью, что упрощает управление периодом отчётности. Отчёт содержит информацию о количестве проживавших клиентов по номерам, количестве номеров на этажах, доходах с номеров за выбранный период. Это важно для аналитики и принятия управленческих решений.

```python
class ReportView(APIView):
    def get(self, request):
        quarter = request.query_params.get('quarter')
        if not quarter:
            return Response({'error': 'Необходимо указать параметр quarter (1-4)'}, status=400)

        try:
            quarter = int(quarter)
            if quarter not in [1, 2, 3, 4]:
                raise ValueError
        except ValueError:
            return Response({'error': 'Параметр quarter должен быть числом от 1 до 4'}, status=400)

        year = datetime.now().year
        if quarter == 1:
            start_date = datetime(year, 1, 1).date()
            end_date = datetime(year, 3, 31).date()
        elif quarter == 2:
            start_date = datetime(year, 4, 1).date()
            end_date = datetime(year, 6, 30).date()
        elif quarter == 3:
            start_date = datetime(year, 7, 1).date()
            end_date = datetime(year, 9, 30).date()
        else:
            start_date = datetime(year, 10, 1).date()
            end_date = datetime(year, 12, 31).date()

        stays_per_room = Stay.objects.filter(
            check_in_date__lte=end_date,
            check_out_date__gte=start_date
        ).values('room__room_id').annotate(client_count=Count('client', distinct=True))

        rooms_per_floor = Room.objects.values('floor').annotate(room_count=Count('room_id'))

        stays = Stay.objects.filter(
            check_in_date__lte=end_date,
            check_out_date__gte=start_date
        )
        income_per_room = {}
        total_income = 0
        for stay in stays:
            actual_check_in = max(stay.check_in_date, start_date)
            actual_check_out = min(stay.check_out_date, end_date)
            duration = (actual_check_out - actual_check_in).days
            income = duration * float(stay.room.cost_per_day)
            income_per_room[stay.room.room_id] = income_per_room.get(stay.room.room_id, 0) + income
            total_income += income

        income_per_room = [{'room_id': room_id, 'total_income': income} for room_id, income in income_per_room.items()]

        report = {
            'stays_per_room': list(stays_per_room),
            'rooms_per_floor': list(rooms_per_floor),
            'income_per_room': income_per_room,
            'total_income': total_income
        }

        return Response(report)
```

### Routes

Для настройки маршрутов используется DefaultRouter из Django REST Framework. Все основные ресурсы размещены по REST-принципам.

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import (
    RoomViewSet, ClientViewSet, StaffViewSet, StayViewSet,
    CleaningScheduleViewSet, CleaningExecutionViewSet
)

router = DefaultRouter()
router.register(r'rooms', RoomViewSet)
router.register(r'clients', ClientViewSet)
router.register(r'staff', StaffViewSet)
router.register(r'stays', StayViewSet)
router.register(r'cleaning-schedules', CleaningScheduleViewSet)
router.register(r'cleaning-executions', CleaningExecutionViewSet)

urlpatterns = router.urls
```
