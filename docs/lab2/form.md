
## CustomUserCreationForm
```python
from django.contrib.auth.forms import UserCreationForm
from .models import User

class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = User
        fields = ('username', 'first_name', 'last_name', 'email',  'password1', 'password2')
```
**Описание**: Эта форма используется для создания нового пользователя.
- **Модель**: `User`
- **Поля**: `username`, `first_name`, `last_name`, `email`, `password1`, `password2`.


## AddCommentForm
```python
from django import forms
from .models import Comments

class AddCommentForm(forms.ModelForm):
    OPTIONS = [
        ('cooperation', 'Cooperation'),
        ('race', 'Race'),
        ('other', 'Other'),
    ]

    com_type = forms.ChoiceField(
        choices=OPTIONS,
        label="Choose option",
        widget=forms.Select,
    )

    class Meta:
        model = Comments
        fields = ['com_type', 'comment']
```
**Описание**: Эта форма используется для добавления комментария.
- **Модель**: `Comments`
- **Поля**: `com_type`, `comment`.
- **Комментарий**: Поле `com_type` предоставляет выбор из предопределенных опций.

## AddDriver
```python
from django import forms
from .models import Driver

class AddDriver(forms.ModelForm):
    class Meta:
        model = Driver
        fields = ['car_id', 'first_name', 'last_name', 'number', 'team', 'experience', 'grade']
```
**Описание**: Эта форма используется для добавления нового водителя.
- **Модель**: `Driver`
- **Поля**: `car_id`, `first_name`, `last_name`, `number`, `team`, `experience`, `grade`.


## AddCar
```python
from django import forms
from .models import Car

class AddCar(forms.ModelForm):
    class Meta:
        model = Car
        fields = '__all__'
```
**Описание**: Эта форма используется для добавления нового автомобиля.
- **Модель**: `Car`
- **Поля**: Все поля модели `Car`.


## AddTruck
```python
from django import forms
from .models import Truck

class AddTruck(forms.ModelForm):
    class Meta:
        model = Truck
        fields = '__all__'
```
**Описание**: Эта форма используется для добавления нового грузовика.
- **Модель**: `Truck`
- **Поля**: Все поля модели `Truck`.


## AddRace
```python
from django import forms
from .models import Race

class AddRace(forms.ModelForm):
    class Meta:
        model = Race
        fields = '__all__'
```
**Описание**: Эта форма используется для добавления новой гонки.
- **Модель**: `Race`
- **Поля**: Все поля модели `Race`.


