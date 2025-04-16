# managers

**Manager** у Django - це спеціальний клас, через який ми взаємодіємо з базою даних. Кожна модель у Django має атрибут `objects`, який є екземпляром `Manager` і дозволяє виконувати запити до бази даних.

1. ***Стандартний Manager***

За замовченням кожна модель у Django має `objects = models.Manager()`.

**Приклад стандартного використання Manager**

```python
from myapps.models import Post 

posts = Post.objects.all() # Отримає всі записи 
latest_post = Post.objects.latest("created_at") # Отримує останній запис 
```

`objects` тут - це **менеджер**, який використовується для виконання ORM-запитів.

***2. Кастомні (власні) менеджери***

Іноді потрібно розширити поведінку `Manager`, наприклад, щоб мати кастомні запити.

**Приклад кастомного менеджеру**

```python
from django.db import models


class PublishedPostManager(models.Manager):
  def get_queryset(self):
    return super().get_queryset().filter(is_publiched=True)


class Post(models.Model):
  title = models.CharField(max_length=255)
  content = models.TextField()
  is_publiched = models.BooleanField(default=False)
  
  # Використання кастомного менеджера 
  objects = models.Manager() # Стандартний
  published = PublishedPostManager() # Кастомний


published_posts = Post.published.all() # Отримаємо тільки опубліковані пости 
```

Тут `Post.published.all()` поверне тільки ті пости, де `is_publiched=True`, тоді як `Post.objects.all()` поверне всі.

***3. Додавання власних методів***

У кастомному `Manager` можна визначити додаткові методи.

**Приклад кастомного менеджера з додатковим методом**

```python
class PostManager(models.Manager):
  def published_in_last_week(self):
    from django.utils.timezone import now, timedelta
    week_ago = now() - timedelta(days=7)
    return super().get_queryset().filter(is_publiched=True, created_at__gte=week_ago)


class Post(models.Model):
  title = models.CharField(max_length=220)
  content = models.TextField()
  is_publiched = models.BooleanField(default=False)
  created_at = models.DateTimeField(auto_now_add=True)

  objects = PostManager() # Використовуємо кастомний менеджер 

# Отримуємо всі пости, опубліковані за останній тиждень
recent_posts = Post.objects.published_in_last_week()
```

`published_in_last_week()` дозволяє отримати тільки ті пости, які були опубліковані протягом 7 днів.

***4. Використання менеджера в QuerySet***

Менеджери можуть працювати разом із кастомним `QuerySet`.

**Приклад використання `QuerySet` у менеджері**

```python
class PostQuerySet(models.QuerySet):
  def published(self):
    return self.filter(is_publiched=True)


class PostManager(models.Manager):
  def get_queryset(self):
    return PostQuerySet(self.model, using=self._db)

  def published(self):
    return self.get_queryset().published()


class Post(models.Model):
  title = models.CharField(max_length=255)
  content = models.TextField()
  is_published = models.BooleanField(default=False)

  objects = PostManager() # Призначаємо кастомний менеджер 

# Використання 
published_posts = Post.objects.published() # Отримуємо тільки опубліковані пости 
```

Тут `published()` працює і як метод `QuerySet`, і як метод `Manager`

***5. Декілька менеджерів у моделі***

Якщо потрібно, можна мати **декілька менеджерів**.

```python
class Post(models.Model):
  title = models.CharField(max_length=255)
  is_published = models.BooleanField(default=False)

  objects = models.Manager() # Стандартний
  published = PublishedPostManager() # Кастомний 
```

Використання:

```python
all_posts = Post.objects.all() # Отримуємо всі пости 
only_published = Post.published.all() # Отримуємо тільки опубліковані   
```

***Висновок***:

 - **Менеджери (`Manager`)** - це спосіб керування запитами до бази даних у Django.
 - Стандартний менеджер - `objects`.
 - Кастомні менеджери дозволяють створювати власну логіку запитів (`select_related()`, `filter()`, тощо).
 - Менеджери можна комбінувати з `QuerySet` для гнучкого використання.
