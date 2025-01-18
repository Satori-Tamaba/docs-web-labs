# Документация к коду

## SkillSerializer
Этот класс используется для сериализации модели `Skill`. Он преобразует данные модели в JSON-формат и обратно.

### Поля:
- `model`: указывает на модель `Skill`.
- `fields`: устанавливает, что все поля модели будут включены.

## SkillOfWarriorSerializer
Аналогичен `SkillSerializer`, но для модели `SkillOfWarrior`.

### Поля:
- `model`: модель `SkillOfWarrior`.
- `fields`: все поля модели.

## WarriorAPIView
Контроллер для работы с объектами `Warrior`.

### Методы:
- `get`: возвращает список всех воинов. Использует сериализатор `WarriorSerializer` для преобразования данных.

## WarriorSerializer
Сериализатор для модели `Warrior`. Включает связанные данные о профессии и скиллах.

### Поля:
- `model`: модель `Warrior`.
- `fields`: все поля модели.
- `profession`: сериализатор для профессии, только для чтения.
- `skills`: сериализатор для скиллов, связанные с моделью `skill`, только для чтения.

## SkillAPIView
Контроллер для работы с объектами `Skill`.

### Методы:
- `get`: возвращает список всех скиллов.
- `post`: добавляет новый скилл. Проверяет валидность данных и сохраняет новый объект.

## ProfessionSerializer
Сериализатор для модели `Profession`. Аналогично другим сериализаторам, включает все поля модели.

### Поля:
- `model`: модель `Profession`.
- `fields`: все поля модели.

## WarriorListAPIView
Контроллер для получения списка всех воинов с их профессиями и скиллами.

### Атрибуты:
- `queryset`: выбирает всех воинов с их профессиями и скиллами.
- `serializer_class`: указывает на использование `WarriorSerializer`.

## WarriorDetailAPIView
Контроллер для получения подробной информации о конкретном воине.

### Атрибуты:
- `queryset`: выбирает конкретного воина с его профессией и скиллами.
- `serializer_class`: использует `WarriorSerializer`.

## WarriorUpdateDeleteAPIView
Контроллер для обновления или удаления информации о воине.

### Атрибуты:
- `queryset`: выбирает всех воинов с их профессиями и скиллами.
- `serializer_class`: использует `WarriorSerializer`.

### Ссылки
```python

urlpatterns = [
    path('warriors/', WarriorAPIView.as_view()),
    path('skills/', SkillAPIView.as_view(), name='skill-list'),
    path('warriors/', WarriorListAPIView.as_view(), name='warrior-list'),
    path('warriors/<int:pk>/', WarriorDetailAPIView.as_view(), name='warrior-detail'),
    path('warriors/<int:pk>/edit/', WarriorUpdateDeleteAPIView.as_view(), name='warrior-edit'),
]

```