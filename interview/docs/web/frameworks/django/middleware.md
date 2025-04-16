# Middleware 

`Middleware` - це проміжний шар, який обробляє запити та відповіді між сервером та вашим додатком. Це серія обробників, які можуть використовувати дії, до того як 
запит потрапить до представлення (**view**) або після того, як представлення сформує відповідь. Вони дозволяють обробляти запити на глобальному рівні, не змінюючи логіку конкретних представлень.

***Основні поняття***:

1. **Що таке `middleware`?** Middleware - це компоненти, які зможуть перехоплювати `HTTP-запити` перед тим, як вони потрапляють до представлення, а також можуть змінювати `HTTP-відповіді` перед тим, як вони будуть відіслані клиєнту. Це своєрідний "ланцюг" обробників, через які проходить кожен запит.

2. **Як працюює Middleware?** Кожен запит `django` - проходить через **серію проміжних обробників** (`middleware`), які пожуть змінювати або фільтрувати запит, додавати заголовки до відповідей або виконувати інші дії. Наприклад, `middleware` може:

  - Перевірити, чи є користувач авторизованим.
  - Додавати кук із сессією.
  - Логувати запити або відповіді.
  - Обробляти помилки, наприклад, перенаправляти користувача у випадку неправильного доступу.

3. **Як працює ланцюг Middleware?**

  - Запит спочатку оброблює **по черзі** через усі `middleware`, які є в налаштуваннях `Django`. Кожен `middleware` може змінювати запит або приймати рішення, щоб передати його далі.
  - Після того, як запит потрапляє до представлення (**view**), `Django` генерує відповідь.
  - Потім відповідь також проходить через middleware, щоб додати заголовки, змінити її або виконати інші операції перед тим, як вона потрапить до клієнта.

  - Ланцюг обов'язків у Django Middleware (це паттерн за яким працює в django):

  Уявимо що у `settings.py` є такі `middleware`:
   
```python 
    MIDDLEWARE = [
      "app.middleware.FirstMiddleware",
      "app.middleware.SecondMiddleware",
      "app.middleware.ThirdMiddleware",
    ]
```
  **Обробка запиту (request)**:
  1. `FirstMiddleware` -> передає запит далі 
  2. `SecondMiddleware` -> передає запит далі 
  3. `ThirdMiddleware` -> передає запит у `view`

  **Обробка відповіді (response)**:
  1. `ThirdMiddleware` -> обробляє відповідь і передає далі 
  2. `SecondMiddleware` -> обробляє відповідь і передає далі
  3. `FirstMiddleware` -> обробляє відповідь і повертає клієнту

4. **Типи middleware**:

  - **Запит (Request) Middleware**: Це `middleware`, яке обробляє запит перед тим, як він потрапить до представлення.
  - **Відповідь (Response) Middleware:** Це `middleware`, яке обробляє віподповідь перед тим, як вона буде передани клієнту.
  - **Помилки (Exception) Middleware:** Це `middleware`, яке обробляє помилки, що виникають під час обробки запиту (наприклад 404, 500 помилки).

5. **Налаштування Middleware**: У `Django` всі `middleware` налаштовуються у файлі `settings.py`, в змінній `MIDDLEWARE`. Це список, що містить класи `middleware`, які будуть застосовуватися до запитів і відповідєй:

```python
MIDDLEWARE = [
  "django.middleware.security.SecurityMiddleware",
  "django.contrib.sessions.middleware.SessionMiddleware",
  "django.middleware.common.CommonMiddleware",
  "django.middleware.csrf.CsrfViewMiddleware",
  "django.contrib.auth.middleware.AuthenticationMiddleware",
]
```
Порядок, в якому вказаний middleware, важливий, оскільки це визначає порядок обробки запиту та відповіді.

6. **Створення власного Middleware:** Щоб створити свій власний `middleware`, потрібно створити клас, який визначатиме два методи:

  - `__init__`: Ініціалізує middleware.
  - `__call__`: або `__get_response__`: Обробляє запит і викликає наступне `middleware`.

Приклад простого middleware:

```python
from django.http import HttpResponse 


class SimpleMiddleware:
  def __init__(self, get_response):
    self.get_response = get_response

  def __call__(self, request):
    response = self.get_response(request)
    return response 
```

7. **Популярні використання Middleware:**

  - Перевірка авторизації і доступу до конкретних сторінок.
  - Додавання заголовків безпеки (наприклад, `Content-Security-Policy`).
  - Логування запитів та відповідей.
  - Реалізація механізму кешування або обробки сессії.
  - Використання мови локалізації

***Приклад роботи Middleware:***
```python
from django.utils.timezone import localtime


class TimezoneMiddleware:
  def __init__(self, get_response):
    self.get_response = get_response

  def __call__(self, request):
    request.timezone = localtime()
    response = self.get_response(request)
    return response

```

***Висновок:***
Middleware є важливою частиною Django, що дозволяє легко впроваджувати загальні функціональності на рівні всього додатку, такі як безпека, сесії, авторизація та інше, без необхідності дублювати код у кожному представленні.
