# Аутентифікація

У django аутентифікація та авторизація реалізується за допомогою вбудованих інструментів, таких як `django.contrib.auth`. Вони дозволяють керувати користувачами, їхними ролями, паролями та доступом до різних частин веб-додатку.

***1. Аутентифікація***

**Аутентифікація** - визначає, хто є користувачем системи. Це перевірка, чи надані користувачем правильні дані для входу.

**Стандартна аутентифікація у Django**

Django вже має вбудовану систему аутентифікації користувачів, яка використовує модель `User` і вбудовані форми для реєстрації, входу та зміну паролів. Базові кроки для налаштування аутентифікації:

***1.1 Налаштування моделі користувача:***

По дефолту, Django використовує модель `User` з `django.contrib.auth.models`. Ця модель містить основні поля: ім'я користувача, email, пароль, роль, та інші.
Якщо треба створити свою модель то можно успадкуватись від `AbstractUser`.

```python 
from django.contrib.auth.models import AbstractUser


class CustomUser(AbstractUser):
  phone_number = models.CharField(max_length=15, null=True, blank=True)
```

Треба вказати свою модель у налаштуваннях проекту (`settings.py`)

```python
AUTH_USER_MODEL = "myapp.CustomUser"
```

***1.2 Форма реєсрації***
Для того, щоб користувач міг зареєструватися, вам потрібно створити форму реєстрації, яка буде зберігати ім'я користувача, імейл, пароль і будь які додаткові поля.

```python
from django import forms 
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User


class SignUpForm(UserCreationForm):
  email = forms.EmailField(max_length=254, required=True)


  class Meta:
    model = User
    fields = ("username", "email", "password1", "password2")
```

***1.3 Перевірка аутентифікації***

Django також має вбудовану аутентифікацію за допомогою `authenticate()` для перевірки введених користувачем даних.

```python
from django.contrib.auth import authenticate, login


def login_view(request):
  if request.method == "POST":
    username = request.POST["username"]
    password = request.POST["password"]

    user = authenticate(request, username=username, password=password)

    if user is not None:
      login(request, user)
      return redirect("home")
    else:
      return render(request, "login.html", {"error": "Invalid credetials"})
  return render(request, "login.html")
```

***1.4 Вихід користувача***

Вихід користувача також реалізовано за допомогою вбудованої функції `logout()`.

```python
from django.contrib.auth import logout

def logout_view(request):
  logout(request)
  return redirect("home")
```

***2. Авторизація***

**Авторизація** - визначає, які дії може виконувати користувач після автентифікації. У Django вона реалізована через **групи** і **права доступу** (permissions), а також за допомогою **департаментів** (місінов).

***2.1 Групи і права доступу***

У Django ви можете визначити ролі через **групи** та **права доступу** (permissions).

***2.2 Групи***
Групи дозволяють об'єднувати користувачів, які мають однакові права доступу.

```python
from django.contrib.auth.models import Group

# Створюємо групу 
admin_group = Group.objects.create(name="Admin")

# Додавання користувача до групи
user.groups.add(admin_group)
```


***2.3 Декоратори для доступу до `views`***

Django має кілька вбудованих декораторів для обмеження доступу до певних вьюшок.

- `@login_required` - дозволяє доступ тільки авторизованим користувачам:

```python
from django.contrib.auth.decorators import login_required

@login_required
def profile_view(request):
  return render(request, "profile.html")
```
- `@premission_required` - дозволяє доступ лише користувачам з певними правами:

```python 
from django.contrub.auth.decorators import premission_required

@premission_required("app.add_model")
def create_model(request):
  return render(request, "create.html")
```

- `@user_passes_test` - дозволяє доступ тільки користувача, які проходять певну перевірку:

```python
from django.contrib.auth.decorators import user_passes_test

@user_passes_test(lambda u: u.is_superuser)
def admin_view(request):
  return render(request, "admin.html")
```

***2.3 Розширена авторизація з групами та привами***

Якщо потрібно, можна додавати більш складну авторизацію, наприклад, користувачі з певними ролями можуть бачити тільки певні ресурси. Для цього використовують механізм **Permission mixins** в класах-поглядах (CBV):

```python
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.views.generic import ListView 


class RestrictedView(LoginRequiredMixin, PermissionRequiredMixin, ListView):
  permission_required = "app.view_model"
  model = MyModel
  template_name = "restricted_list.html"
```

***3. Токен аутентентифікація***

Іноді для API потрібно використовувати токени для аутентифікації, наприклад, за допомогою **DRF (Django Rest Framework)** або **JWT (JSON Web Tokens)**.

***3.1 Використання Django Rest Framework (DRF)***

DRF має вбудовану підтримку для токенної аутентифікації через `TokenAuthentication`. Ви можете реалізувати аутентифікацію токенами таким чином:

1. Встановіть `djangorestframewok` та `djangorestframewok-authtoken`:

```bash 
pip install djangorestframewok djangorestframewok-authtoken
```

2. Додати до `INSTALLED_APPS` у файлі `settings.py`:

```python
INSTALLED_APPS = [
  ...
  "rest_framework",
  "rest_framework.authtoken",
]
```

3. Створіть токен для користувача через адмінку або команду:

```bash 
python manage.py drf_create_token <username>
```

4. Включіть токенну аутентифікацію в API:

```python 
from rest_framework.authentication import TokenAuthentication
from rest_framework.permissions import IsAuthenticated 
from rest_framework.views import APIView 
from rest_framework.response import Response 


class MyApiView(APIView):
  authentication_classes = [TokenAuthentication]
  permission_classes = [IsAuthenticated]

  def  get(self, request):
    return Response({"message": "Hello, world!"})
```

***Висновок***

У Django є потужна система аутентифікації та авторизації, що дозволяє налаштувати різні рівні доступу для користувачів. Ви можете використовувати вбудовані інструменти для стандартної аутентифікації або використовувати більш складні механізми, такі як токенна аутентифікація для API.
