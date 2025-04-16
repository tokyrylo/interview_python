# Тестування

Django підтримує тестування через вбудований модуль `unittest`, але також дозволяє використовувати `pytest` для зручнішого написання тестів.

***Основні типи тестування для Django:***

1. **Unit-тести** - перевіряють окремі функції або методи.
2. **Інтеграційні тести** - перевіряють взаємодію між компонентами (наприклад, роботу моделей та бази даних).
3. **Функціональні тести** - емулірують реальні сценарії використання застосунку через HTTP-запити.

***1. Як пишуться тести у Django***
Django створює файл `tests.py` всередені кожного додатку. Також можна створити окрему папку `tests/` з різними файлами для кращої організації тестів.

**Приклад структури тестів:**

```bash 
myapp/
|- tests/
|  |- test_models.py 
|  |- test_views.py 
|  |- test_forms.py
|  |- test_urls.py 
```

***1.1 Запуск тестів***
Тести можна запустити командою:
```bash 
python manage.py test 
```
Або, якщо  використовується `pytest`:

```bash 
pytest 
```
***2. Unit-тести***
Unit-тести перевіряють окремі функції або методи без залежностей.

**Приклад тестування моделі**

```python
from django.test import TestCase
from myapp.models import CustomUser


class CustomUserTest(TestCase):
  def test_create_user(self):
    user = CustomUser.objects.create(email="test@example.com", first_name="John")
    self.assertEqual(user.email, "test@example.com")
    self.assertEqual(user.first_name, "John")
```

**Приклад тестування форм**

```python
from django.test import TestCase
from myapp.forms import CustomUserCreationForm

class CustomUserCreateionFormTest(TestCase):
  def test_valid_form(self):
    form_data = {
      "email": "test@example.com", 
      "first_name": "John", 
      "password1": "TestPass123!", 
      "password2": "TestPass123!",
    }
    form = CustomUserCreationForm(data=form_data)
    self.assertEqual(form.is_valid())
```

***3. Інтеграційні тести***
Перевіряють, які різні частини застосунку взаємодіють між собою, наприклад, моделі та база даних.

**Приклад тестування моделі та бази даних**

```python
from django.test import TestCase
from myapp.models import CustomUser


class DatabaseIntegrationTest(TestCase):
  def test_user_creation_and_retrival(self):
    user = CustomUser.objects.create(email="admin@example.com", first_name="Admin")
    retrived_user = CustomUser.objects.get(email="admin@example.com")
    self.assertEqual(retrived_user.first_name, "Admin")
```

***4. Функціональні тести***

Функціональні тести використовують Django `TestCase` для емуляції реального користувача.

**Приклад тестування представлення (views)**

```python 
from django.test import TestCase
from django.urls import reverse
from myapp.models import CustomUser


class UserViewTest(TestCase):
  def setUp(self):
    self.user = CustomUser.objects.create(
      email="test@example.com",
      first_name="John",
    )

  def test_user_list_view(self):
    response = self.client.get(reverse("user_list"))
    self.assertEqual(response.status_code, 200)
    self.assertEqual(response, "John")
```
**Приклад тестування API (якщо використовуюєте Django REST Framework)**

```python
from rest_framework.test import APITestCase
from django.urls import reverse
from myapp.models import CustomUser


class UserAPITest(APITestCase):
  def setUp(self):
    self.user = CustomUser.objects.create(
      email="test@example.com",
      first_name="John",
    )

  def test_get_users(self):
    url = reverse("api_user_list")
    response = self.client.get(url)
    self.assertEqual(response.status_code, 200)
    self.assertEqual(response.data[0]["email"], "test@example.com")
```
***5. Використання `pytest` для тестування***

Якщо вам потрібен бульш зручний синтаксис для тестування, використовуйте `pytest`:

**Встановлення:**

```bash 
pip install pytest pytest-django
```
**Додайте конфігурацію у `pytest.ini`**

```ini
[pytest]
DJANGO_SETTINGS_MODULE = myproject.settings 
```
**Приклад тестування за допомогою `pytest`**
```python 
import pytest 
from myapp.models import CustomUser


@pytest.mark.django_db
def test_create_user():
  user = CustomUser.objects.create(
    email="test@example.com",
    first_name="John",
  )
  assert user.email == "test@example.com"
```

Запуск:
```bash 
pytest 
```
***6. Тестування асинхронних (async) функцій у Django***
```python 
from django.test import AsyncTestCase
from myapp.models import CustomUser


class AsyncUserTest(AsyncTestCase):
  async def test_create_user(self):
    user = await CustomUser.objects.acreate(
      email="test@example.com",
      first_name="John",
    )
    self.assertEqual(user.email, "test@example.com")
```

***7. Mock-тестування***

Якщо ваш код взаємодіє із зовнішніми API, використовуйе `unittest.mock`:

```python
import requests

from unittest.mock import patch
from django.test import TestCase

def fetch_data():
  response = request.get("https://api.example.com/data")
  return response.json()


class MockTestCase(TestCase):
  @patch("requests.get")
  def test_fetch_data(self, mock_get):
    mock_get.return_value.json.return_value = {"key": "value"}
    result = fetch_data()
    self.assertEqual(result, {"key": "value"})
```

***Висновок***

- **Unit-тести** перевіряють окремі функції та методи.
- **Інтеграційні тести** перевіряють взаємодію моделей та бази даних.
- **Функціональні тести** емулятують HTTP-запити до представлень.
- **Використовуйте** `pytest` для більш зручного тестування.
- **Mock-тестування** допомагає тестувати API-запити без реальних викликів.

Завжди пишіть тести, щоб покращити стабільність застосунку!
