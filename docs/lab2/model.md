# Модели

## Car
```Python
class Car(models.Model):
    model = models.CharField(max_length=20)
    create_date = models.DateField(blank=True)
    Acceleration_to_100 = models.FloatField()
    weight = models.IntegerField()

    def __str__(self):
        return self.model
```
Описание: Модель автомобиля.
Поля:

    model: Название модели автомобиля.
    create_date: Дата выпуска.
    Acceleration_to_100: Время разгона до 100 км/ч.
    weight: Вес автомобиля.

Методы:
## Driver
    __str__(): Возвращает название модели автомобиля.
```Python
class Driver(models.Model):
    car_id = models.ForeignKey(Car, on_delete=models.CASCADE)
    first_name = models.CharField(max_length=15)
    last_name = models.CharField(max_length=15)
    number = models.IntegerField()
    team = models.CharField(max_length=30)
    experience = models.IntegerField()
    GRADE_CHOICES = [
        ('rookie', 'Новичок'),
        ('pro', 'Профессионал'),
        ('master', 'Мастер'),
    ]

    grade = models.CharField(max_length=15, choices=GRADE_CHOICES)
    slug = models.SlugField(unique=True, max_length=200, blank=True)

    def __str__(self):
        return f"{self.first_name} {self.last_name}"

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(f"{self.first_name}_{self.last_name}")
        super().save(*args, **kwargs)
```
Описание: Модель водителя.
Поля:

    car_id: Ссылка на автомобиль (связанное поле).
    first_name, last_name: Имя и фамилия водителя.
    number: Номер водителя.
    team: Название команды.
    experience: Опыт водителя в годах.
    grade: Уровень навыков (выбор из списка).
    slug: Уникальный текстовый идентификатор.

Методы:

    __str__(): Возвращает полное имя водителя.
    save(): Создает slug на основе имени и фамилии

## Truck
```Python
class Truck(models.Model):
    name = models.CharField(max_length=20)
    city = models.CharField(max_length=20)
    country = models.CharField(max_length=20)
    date = models.DateField(blank=True)
    truck_type = models.CharField(max_length=20)

    def __str__(self):
        return self.name
```
Описание: Модель трассы.
Поля:

    name: Название трассы.
    city, country: Город и страна.
    date: Дата выпуска.
    truck_type: Тип трассы.

Методы:

    __str__(): Возвращает название грузовика.

## User
```Python
class User(AbstractUser):
    first_name = models.CharField(max_length=20)
    last_name = models.CharField(max_length=20)
    slug = models.SlugField(unique=True, max_length=200, blank=True)
    username = models.CharField(
        max_length=22,
        unique=True,
        null=False
    )

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.username)
        super().save(*args, **kwargs)
```
Описание: Пользовательская модель, расширяющая стандартную модель Django AbstractUser.
Поля:

    first_name, last_name: Имя и фамилия пользователя.
    slug: Уникальный текстовый идентификатор.
    username: Уникальное имя пользователя.

Методы:

    save(): Создает slug на основе имени пользователя.

## Race
```Python
class Race(models.Model):
    truck_id = models.ForeignKey(Truck, on_delete=models.CASCADE)
    Driver_id = models.ForeignKey(Driver, on_delete=models.CASCADE)
    start_position = models.IntegerField()
    end_position = models.IntegerField()
    driver_time = models.TimeField()
```

Описание: Модель гонки.
Поля:

    truck_id: Ссылка на грузовик (связанное поле).
    Driver_id: Ссылка на водителя (связанное поле).
    start_position: Стартовая позиция водителя.
    end_position: Конечная позиция водителя.
    driver_time: Время гонки для водителя.

## Comments
```Python
class Comments(models.Model):
    user_id = models.ForeignKey(User, on_delete=models.CASCADE)
    race_id = models.ForeignKey(Race, on_delete=models.CASCADE)
    com_type = models.CharField(max_length=30)
    date = models.DateField()
    comment = models.TextField()
```
Описание: Модель комментариев.
Поля:

    user_id: Ссылка на пользователя, оставившего комментарий.
    race_id: Ссылка на гонку, к которой относится комментарий.
    com_type: Тип комментария.
    date: Дата комментария.
    comment: Текст комментария.