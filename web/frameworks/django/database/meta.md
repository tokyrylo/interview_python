# Meta class 

У Django кожна модель може мати вкладений клас `Meta`, який використовується для налаштування поведінки моделі. Своєрідна конфігурація моделі.

***Основні можливості `Meta` класу***
1. **Зміна назви таблиці у базі даних (`db_table`)**
За замовченням Django автоматично створює назву таблиці, поєднуючи ім'я додатку та ім'я моделі (наприклад, `myapp_mymodel`). Але можна вручну задати назву таблиці:

```python 
class UserProfile(models.Model):
  user = models.OneToOneField(User, on_delete=models.CASCADE)
  bio = models.TextField()

  class Meta:
    db_table = "user_profiles" # Назва в таблиці 
```
***2. Впорядкування результатів (`ordering`)***
Дозволяє вказати поряд сортування записів за замовченням.

```python 
class Article(models.Model):
  title = models.CharField(max_length=255)
  created_at = models.DateTimeField(auto_now_add=True)

  class Meta:
    ordering = ["-created_at"] # Сортування за датою (від новіших до старіших)
```

***3. Унікальні обмеження (`unique_together`)***
Забезпечує унікальність комбінації значень у двох або більше полях

```python 
class Membership(models.Model):
  user = models.ForeignKey(User, on_delete=models.CASCADE)
  group = models.ForeignKey(Group, on_delete=models.CASCADE)

  class Meta:
    unique_together = ("user", "group") # Один користувач і одна група 
```
**! Примітка:** У нових версіях Django рекомендується використовувати `UniqueConstraint`

***4. Абстрактні моделі (`abstract=True`)***
Якщо потрібно створити "шаблонну" модель, яка не буде мати всластну таблицю в БД, але може бути успадкована іншими моделями.

```python 
class BaseModel(models.Model):
  created_at = models.DateTimeField(auto_now_add=True)
  updated_at = models.DateTimeField(auto_now=True)


  class Meta:
    abstract = True # Ця модель не буде створювати таблицю в БД.

class Product(BaseModel):
  name = models.CharField(max_length=100)
```

***5. Встановлення прав доступу (`permissions`)***
Можна створювати власні права доступу для адміністраторів або інших користувач

```python
class Order(models.Model):
  user = models.ForeignKey(User, on_delete=models.CASCADE)
  total_price = models.DecimalField(max_digits=10, decimal_places=2)


  class Meta:
    permissions = [
      ("can_view_orders", "Can view orders"),
      ("can_edit_orders", "Can edit orders"),
    ]
```
Тепер можна використовувати ці дозволи у **перевірках доступу**.

***6. Унікальні обмеження на рівні БД (`constraints`)***
Сучасний спосіб задати унікальність, ніж `unique_together`

```python 
from django.db.models import UniqueConstraint


class Enrollment(models.Model):
  student = models.ForeignKey(User, on_delete=models.CASCADE)
  course = models.ForeignKey(Course, on_delete=models.CASCADE)


  class Meta:
    constraints = [
      UniqueConstraint(fields=["student", "course"], name="unique_student_course")
    ]
```
***7. Налаштування `verbose_name` i `verbose_name_plural`***
Змінює назву моделі в адмінці.

```python 
class Book(models.Model):
  title = models.CharField(max_length=255)

  class Meta:
    verbose_name = "Книга"
    verbose_name_plural = "Книги"
```

***Висновок***

 - Сортування записів (`ordering`)
 - Унікальних обмежень (`unique_together`, `constraints`)
 - Створення абстрактних моделей (`abstract=True`)
 - Додавання власних прав (`permissions`)
 - Покращення вигляду в адмінці (`verbose_name`)
