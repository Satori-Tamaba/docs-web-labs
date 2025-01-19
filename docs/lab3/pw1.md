# Практика 1
Цель работы: получить представление о работе с запросами в Django ORM.

Сделать запрос на создание 6-7 новых автовладельцев и 5-6 автомобилей, каждому автовладельцу назначить удостоверение и от 1 до 3 автомобилей
## Задание 1
```python
car1 = Car.objects.create(car_number="X001YZ99", brand="Tesla", model="Model S", color="Blue")
car2 = Car.objects.create(car_number="Y002XZ88", brand="Chevrolet", model="Malibu", color="Black")
car3 = Car.objects.create(car_number="Z003XY77", brand="Hyundai", model="Elantra", color="White")
.....................................
owner1 = CarOwner.objects.create(first_name="Chris", last_name="Evans", passport_number="9876543211", nationality="Brazil")
owner2 = CarOwner.objects.create(first_name="Laura", last_name="Smith", passport_number="1231231234", nationality="Italy")
owner3 = CarOwner.objects.create(first_name="Daniel", last_name="Brown", passport_number="4564564567", nationality="Japan")
..................................
DrivingLicence.objects.create(car_owner=owner1, license_number="DL54321", license_type="C", issue_date=date(2018, 3, 10))
DrivingLicence.objects.create(car_owner=owner2, license_number="DL65432", license_type="C", issue_date=date(2019, 7, 25))
DrivingLicence.objects.create(car_owner=owner3, license_number="DL76543", license_type="D", issue_date=date(2020, 9, 15))

ownership1 = Ownership.objects.create(
    car_owner=owner1,
    car=car1,
    start_date=date(2021, 5, 15),
    end_date=date(2021, 5, 15) + timedelta(days=180)
)

ownership2 = Ownership.objects.create(
    car_owner=owner2,
    car=car2,
    start_date=date(2020, 8, 10),
    end_date=date(2020, 8, 10) + timedelta(days=365)
)

ownership3 = Ownership.objects.create(
    car_owner=owner3,
    car=car3,
    start_date=date(2022, 2, 20),
    end_date=date(2022, 2, 20) + timedelta(days=300)
)
```
## Задание 2
Обновим модели сделаем миграции

### 1  Вывести все машины марки “Tesla” 
```python
cars_tesla = Car.objects.filter(brand="Tesla").values("car_number", "brand", "model", "color")
```
### 2  Найти всех водителей с именем “Daniel” 
```python
drivers_named_daniel = CarOwner.objects.filter(first_name="Daniel").values("first_name", "last_name", "passport_number", "nationality")
```

### 3 Взять любого случайного владельца, получить его id, и по этому id получить экземпляр удостоверения в виде объекта модели
```python
random_owner = CarOwner.objects.first()
driving_license = DrivingLicence.objects.filter(car_owner_id=random_owner.id).values("license_number", "license_type", "issue_date").first()
```
### 4 Вывести всех владельцев красных машин
```python
owners_of_blue_cars = CarOwner.objects.filter(ownership__car__color="Blue").values("first_name", "last_name", "passport_number", "nationality").distinct()
```
### 5 Найти всех владельцев, чей год владения машиной начинается с 2021 
```python
owners_from_2021 = CarOwner.objects.filter(ownership__start_date__year=2021).values("first_name", "last_name", "passport_number", "nationality").distinct()
```
## Задание 3
### 1 Вывод даты выдачи самого старшего водительского удостоверения

```python
oldest_license_date = DrivingLicence.objects.aggregate(oldest_date=Min('issue_date'))
```
### 2 Укажите самую позднюю дату владения машиной, имеющую какую-то из существующих моделей в вашей базе
```python
latest_ownership_date = Ownership.objects.aggregate(latest_date=Max('end_date'))
```

### 3 Выведите количество машин для каждого водителя
```python
drivers_with_car_count = CarOwner.objects.annotate(car_count=Count('ownerships__car')).distinct()
```
### 4 Подсчитайте количество машин каждой марки

```python
brand_car_count = Car.objects.values('brand').annotate(count=Count('id'))
```
### 5 Отсортируйте всех автовладельцев по дате выдачи удостоверения

```python
drivers_sorted_by_license_date = CarOwner.objects.filter(
    ownerships__drivinglicence__isnull=False
).order_by('ownerships__drivinglicence__issue_date').distinct()
```