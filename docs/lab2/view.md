# Django Views 


## 1. Вспомогательная функция: `is_admin`
Функция `is_admin` проверяет, является ли пользователь администратором (суперпользователем).

```python
def is_admin(user):
    return user.is_superuser
```

# Представление: show_user

Это представление отображает детали конкретного пользователя по его slug.
```Python
def show_user(request, user_slug):
    user = get_object_or_404(User, slug=user_slug)
    return render(request, 'score/show_user.html', {'user': user})
```
## Представление: show_driver

Отображает информацию о конкретном водителе, включая его автомобиль.
```Python
def show_driver(request, driver_slug):
    driver = get_object_or_404(Driver, slug=driver_slug)
    car = driver.car_id
    return render(request, 'score/driver_details.html', {'driver': driver, 'car': car})
```
## Представление: show_drivers

Это представление показывает список водителей с пагинацией и возможностью поиска по имени.
```Python
def show_drivers(request):
    query = request.GET.get('q', '')
    drivers_list = Driver.objects.all().order_by('id')
    if query:
        drivers_list = drivers_list.filter(first_name__icontains=query) | drivers_list.filter(last_name__icontains=query)
    paginator = Paginator(drivers_list, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, 'score/drivers.html', {'page_obj': page_obj, 'query': query})
```
## Представление: detail_race

Отображает подробную информацию о гонке, включая сортировку, пагинацию и комментарии. Пользователи могут добавлять комментарии через форму.
```Python
def detail_race(request, race_id):
    sort = request.GET.get('sort', 'end_position')
    order = request.GET.get('order', 'asc')
    sort_order = sort if order == 'asc' else f'-{sort}'
    races = Race.objects.filter(truck_id=race_id).select_related('Driver_id', 'truck_id').order_by(sort_order)
    paginator = Paginator(races, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    if request.method == 'POST':
        form = AddCommentForm(request.POST)
        if form.is_valid():
            comment = form.save(commit=False)
            race_instance = get_object_or_404(Race, pk=race_id)
            comment.race_id = race_instance
            comment.user_id = request.user
            comment.date = timezone.now()
            comment.save()
            return redirect('detail_race', race_id=race_id)
    else:
        form = AddCommentForm()
    comments = Comments.objects.filter(race_id=race_id)
    return render(request, 'score/race_details.html', {'page_obj': page_obj, 'form': form, 'comments': comments, 'sort': sort, 'order': order})
```
## Представление для админа: add_driver

Представление, которое позволяет администратору добавлять нового водителя. Доступно только пользователям с правами администратора.
```Python
@user_passes_test(is_admin)
def add_driver(request):
    if request.method == 'POST':
        form = AddDriver(request.POST)
        if form.is_valid():
            driver = form.save()
            return redirect('show_driver', driver_slug=driver.slug)
    else:
        form = AddDriver()
    return render(request, 'score/new_object.html', {'form': form})
```
## Представление для админа: add_car

Представление, которое позволяет администратору добавлять новый автомобиль. Доступно только пользователям с правами администратора.
```Python
@user_passes_test(is_admin)
def add_car(request):
    if request.method == 'POST':
        form = AddCar(request.POST)
        if form.is_valid():
            form.save()
            return redirect('drivers')
    else:
        form = AddCar()
    return render(request, 'score/new_object.html', {'form': form})
```
## Представление для админа: add_race

Представление для добавления новой гонки. Доступно только администратору.
```Python
@user_passes_test(is_admin)
def add_race(request):
    if request.method == 'POST':
        form = AddRace(request.POST)
        if form.is_valid():
            form.save()
            return redirect('races')
    else:
        form = AddRace()
    return render(request, 'score/new_object.html', {'form': form})
```
## Представление для админа: add_truck

Представление для добавления нового трека. Доступно только администратору.
```Python
@user_passes_test(is_admin)
def add_truck(request):
    if request.method == 'POST':
        form = AddTruck(request.POST)
        if form.is_valid():
            form.save()
            return redirect('races')
    else:
        form = AddTruck()
    return render(request, 'score/new_object.html', {'form': form})
```
## Представление: show_races
```Python
Отображает список всех гонок.

def show_races(request):
    races = Truck.objects.all()
    return render(request, 'score/show_races.html', {'races': races})
```
## Представление: show_users

Отображает список пользователей с пагинацией.
```Python
def show_users(request):
    users = User.objects.all().order_by('id')
    paginator = Paginator(users, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, 'score/users.html', {'page_obj': page_obj})
```
## Представление: profile_view

Отображает профиль текущего пользователя. Требуется авторизация.
```Python
@login_required
def profile_view(request):
    return render(request, 'score/profile.html', {'user': request.user})
```
## Представление для регистрации: signup

Представление для регистрации нового пользователя.
```Python
def signup(request):
    if request.method == 'POST':
        form = CustomUserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('races')
    else:
        form = CustomUserCreationForm()
    return render(request, 'score/signup.html', {'form': form})
```
## Класс для входа: CustomLoginView

Класс для настройки шаблона страницы входа.
```Python
class CustomLoginView(LoginView):
    template_name = 'score/login.html'
```