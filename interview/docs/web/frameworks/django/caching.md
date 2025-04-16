# Кешування 

**Кешування** у Django дозволяє зменшити навантаження на сервер і базу даних, зберігаючи часто використовувані дані в пам'яті або інших швидких середовищах.

***1. Налаштування кешування Django***

Спочатку треба налаштувати бекенд кешу в `settings.py`. Django підтримує кілька варіантів:

**Файловий кеш**

Зберігає кеш у файлах на диску:

```python 
CACHES = {
  "default": {
    "BACKEND": "django.core.cache.backends.filebased.FileBasedCache",
    "LOCATION": "/var/tmp/django_cache" # Папка для кешу 
  }
}
```

**Кеш у пам'яті (Memcached)**
Швидке кешування в оперативній пам'яті:

```python 
CACHES = {
  "default":
    "BACKEND": "django.core.cache.backends.memcached.MemcachedCache",
    "LOCATION": "127.0.0.1:112211",
}
```
**Кеш у Redis**

**Redis** - один із найшвидших варіантів кешування:

```python
CACHES = {
  "default":
    "BACKEND":"django.core.cache.backends.redis.RedisCache",
    "LOCATION": "redis://127.0.0.1:6379/0",
}
```
**Кеш у базі даних**
Якщо немає можливості використовувати **Redis** або **Memcached**

```python
CACHES = {
  "default":
    "BACKEND": "django.core.cache.backends.db.DatabaseCache",
    "LOCATION": "my_cache_table",
}
```
Перед використанням треба створити таблицю в бд за допомогою команди:

```bash 
python manage.py createcachetable
```

***2. Типи кешування у Django***

**2.1 Кешування всього сайту**

Кешує всі сторінки у Django. У `settings.py` додаємо:

```python
MIDDLEWARE = [
  "django.middleware.cache.UpdateCacheMiddleware", # Оновлює кеш 
  "django.middleware.common.CommonMiddleware",
  "django.middleware.cache.FetchFromCacheMiddleware", # Дістає з кешу 
]
CACHE_MIDDLEWARE_SECONDS = 600 # Час життя кешу (10 хвилин)
```

**2.2 Кешування окремих сторінок**

Декоратор `@cache_page()` кешує окрему сторінку:

```python 
from django.views.decorators.cache import cache_page 

@cache_page(60 * 15) # 15 хвилин кешу
def my_view(request):
  return HttpResponse("This page is caching!")
```

**2.3 Кешування фрагментів сторінки**
Іноді потрібно кешувати лише частину HTML-шаблону.

```html 
{% load static %}
{% cache 300 sidebar %}

  <div>Cached content</div>
{% endcache %}
```
**2.4 Кешування запитів у базу даних**
Щоб не використовувати повторні запити, можна кешувати їх:

```python 
from django.core.cache import cache 

def get_data():
  data = cache.get("my_data") # Перевіряємо кеш 
  if not data:
    data = MyModel.objects.all() # Запит до бази 
    cache.set("my_data", data, 300) # Зберігаємо у кеш на 5 хвилин 
  return data 
```

**2.5 Кешування на рівні низькорівневого API**
Django надає `cache.get()` і `cache.set()` для работи з кешем:

```python 
from django.core.cache import cache 

cache.set("key", "value", timeout=60) # Зберігаємо на 1 хвилину 
value = cache.get("key") # Отримуємо значення 
cache.delete("key") # Видаляємо кеш 
```

***3. Очистка кешу***

Можна видалити всі кешовані дані командою 

```bash 
python manage.py clearcache 
```

Або, в коді:

```python 
cache.clear()
```

***Висновки***
Кешування у Django - потужний механізм для оптимізації продуктивності:

  - **Кеш всього сайту** – для глобального кешування.
  - **Кеш окремих сторінок** – для конкретних представлень.
  - **Кешування фрагментів шаблонів** – коли треба кешувати лише частину сторінки.
  - **Кеш запитів до бази** – щоб уникати повторних звернень.
