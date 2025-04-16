# Signals

**Signals** - це механизм, що дозволяє різними частинами додатку редагувати на певні події без необхідності безпосередньо змінювати код основної логіки.
Це корисно для підтримки слабкого зв'язку між компонентами системи.

***Що таке Django Signals***
Django - має вбудовану систему сигналів, яка дозволяє виконування код після або перед, певними подіями (наприклад, створення, оновлення або видалення об'єкту моделі).

***Основні концепції Signals***

 - **Відправник (Sender)** - об'єкт, який генерує сигнал (наприклад, модель, що зберігається).
 - **Сигнал (Signal)** - подія, що відбувається (наприклад, `pre_save`, `post_save`)/
 - **Приймач (Receiver)** - функція, яка виконується у відповідь на сигнал.
 - **Декоратор `@receiver`** - використовується для прив'язки функції до сигналу.

***Вбудовані сигнали у Django***

Django надає набір стандартних сигналів, найпопулярніші з яких:

**Сигнали для моделей `django.db.models.signals`**

| Сигнал        | Коли використовується |
| -------------- | --------------------- |
| `pre_save` | Перед збереженням об'єкту   |
| `post_save` | Після збереження об'єкту   |
| `pre_delete` | Перед видаленням об'єкту  |
| `post_delete` | Після видалення об'єкту |  
| `m2m_changed` | При зміні зв'язку `ManyToManyField` |

**Сигнали для запитів `django.core.signals`**

| Сигнал           | Опис                                             |
| ---------------- | ------------------------------------------------ |
| `request_started`   | Перед обробкою запиту                         |
| `request_finished`    | Після обробки запиту                        |
| `got_request_exception` | Якщо під час обробки запиту виникла помилка |


***Використання Signals на прикладі:***
**Приклад `post_save`: створення профілю користувача після реєстрації**
Припустимо, у нас є модель `UserProfile`, яку потрібно автоматично створювати при реєстрації нового користувача.

**Створюємо сигнал у `signals.py`:**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

from .models import UserProfile


@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
  if created:
    UserProfile.objects.create(user=instance)
```

 - `@receiver(post_save, sender=User)` - визначаємо, що функція `create_user_profile` буде запускатися після збереження об'єкту `User`.
 - Перевіряємо, чи `created == True`, щоб сигнал виконується лише при створенні нового користувача.

**Реєструємо сигнал у `apps.py`:**
Щоб Djnago знав про сигнал, додайте імпорт `signals.py` у ваш додаток `myapp/apps.py`.

```python
from django.apps import AppConfig


class MyAppConfig(AppConfig):
  default_auto_field = "django.db.models.BigAutoField"
  name = "myapp"

  def ready(self):
    try:
      import myapp.signals
    except:
      ImportError
```

***Інші приклади Signals***
  
**Автоматичне видалення пов'язаних об'єктів (`post_delete`):**
При виделенні користувача видаляємо і його профіль:

```python
from django.db.models.signals import post_delete


@receiver(post_delete, sender=User)
def delete_user_profile(sender, instance, **kwargs):
  if instance.profile:
    instance.profile.delete()
```

**Редагування поля перед збереженням (`pre_save`)**
Якщо хочемо автоматично змінити значення перед збереженням об'єкту

```python
from django.db.models.signals import pre_save
from django.utils.text import slugify

from .models import Article


@receiver(pre_save, sender=Article)
def generate_slug(sender, instance, **kwargs):
  if not instance.slug:
    instance.slug = slugify(instance.title)

```

***Висновок:***

- Signals у Django дозволяють реагувати на події (створення, оновлення, видалення об'єктів) без зміни логіки основних моделей та предствалень.
- Використовується для автоматизації, логування, перевірок та інших фонових завдань.
- Для роботи з сігналами потрібно реєструвати їх у `apps.py`.
- Django містить набір вбудованих сигналів (`post_save`, `pre_save`, `post_delete`), але можна створювати власні.
