# Зв'язки у Django ORM

1. ***One-to-Many (Один для багатьох)*** - `ForeignKey`

- **Опис**:

Цей зв'язок означає, що **один об'єкт** моделі може бути пов'язаний **з багатьма об'єктами** іншої моделі.

- **Використовується:** `models.ForeignKey`

**Приклад:**
Один **автор** може написати **багато книг**, але **книга** має лише одного **автора**.

```python
from django.db import models


class Author(models.Model):
  name = models.CharField(max_length=255)


class Book(models.Model):
  title = models.CharField(max_length=200)
  author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")
```
**Як отримати всі книги автора?**

```python
author = Author.objects.get(id=1)
books = author.books.all()
```

**Як отримати автора конкретної книги?**

```python
book = Book.objects.get(id=1)

author = book.author
```

2. ***One-to-One (Один до одного)*** - `OneToOneField`

- **Опис:**

Цей зв'язок означає, що **один об'єкт** першої моделі може мати **тільки один відповідний об'єкт** другої моделі.

- **Використовується:** `models.OneToOneField` 

**Приклад:**
Кожен користувач має **лише один профіль**.

```python
class User(models.Model):
  username = models.CharField(max_length=100)


class Profile(models.Model):
  user = models.OneToOneField(User, on_delete=models.CASCADE, related_name="profile")
  bio = models.TextField()
```

**Як отримати профіль користувача?**

```python
user = User.objects.get(id=1)
profile = user.profile
```

**Як отримати користувача з профілю**

```python
profile = Profile.objects.get(id=1)
user = profile.user
```

3. ***Many-to-Many (Багато до багатьох)*** - `ManyToManyField`
- **Опис:**
Цей зв'язок означає, що **один об'єкт** першої моделі бути пов'язаний **з багатьма об'єктами** другоъ моделі, і навпаки.
- **Використовується:** - `models.ManyToManyField`

**Приклад:**
Один студент може записуватися на **декілька курсів**, і один курс може мати **декілька студентів**.


```python
class Student(models.Model):
  name = models.CharField(max_length=100)


class Cource(models.Model):
  title = models.CharField(max_length=200)
  students = models.ManyToManyField(Student, related_name="courses")
```

**Як отримати всі курси студента?**

```python
student = Student.objects.get(id=1)
courses = student.courses.all()
```

**Як отримати всіх студентів, що записані на курс?**

```python
cource = Cource.objects.get(id=1)
students = cource.students.all()
```

4. ***Self-referential (Зв'язок із собою)***

- **Опис:**
Цей тип зв'язку використовується, коли модель має **зв'язок сама з собою**.
- **Використовується:** `ForeignKey`, `OneToOneField`, `ManyToManyField`
**Приклад 1 (дерево категорій, `ForeignKey`)**
Одна категорія може мати **багато підкатегорій**, але **підкатегорія** належить лише до **однієї** категорії.

```python
class Category(models.Model):
  name = models.CharField(max_length=100)
  parent = models.ForeignKey("self", on_delete=models.CASCADE, null=True, blank=True, related_name="subcategories")
```

**Отримати всі підкатегорії певної категорії:**

```python
category = Category.objects.get(id=1)
subcategories = category.subcategories.all()
```

**Приклад 2 (зв'язок один-до-одного, `OneToOneField`)**
Один співробітник може мати одного керівника (який теж є співробітником).
```python
class Employee(models.Model):
  name = models.CharField(max_length=100)
  manager = models.OneToOneField("self", on_delete=models.SET_NULL, null=true, blank=True)
```

**Приклад 3 (зв'язок багато-до-багатьох, `ManyToManyField`)**
Користувач може мати багато друзів, а кожен друг теж є користувачем.

```python
class User(models.Model):
  name = models.CharField(max_length=100)
  friends = models.ManyToManyField("self", blank=True)
```

5. ***Проміжна таблиця для `ManyToManyField`***

У `ManyToManyField` Django автоматично створює **проміжну таблицю**, але можна її налаштувати вручну.
**Приклад:**
Курс і студент можуть мати додаткову інформацію про реєстрацію (наприклад, дату запису).

```python
class Student(models.Model):
  name = models.CharField(max_length=100)


class Cource(models.Model):
  title = models.CharField(max_length=200)
  students = models.ManyToManyField(Student, through="Enrollment")


class Enrollment(models.Model):
  student = models.ForeignKey(Student, on_delete=models.CASCADE)
  cource = models.ForeignKey(Cource, on_delete=models.CASCADE)
  enrollment_date = models.DateTimeField(auto_now_add=True)
```

**Отримати дату запису студенту на курс:**

```python
enrollment = Enrollment.object.get(student=student, cource=cource)
print(enrollment.enrollment_date)
```

6. ***Додаткові параметри зв'язків:***

| Параметр                        | Опис                                                              |
| ------------------------------- | ----------------------------------------------------------------- |
| `on_delete=models.CASCADE`        | Видаляє зв'язані записи, якщо батьківський запис видалено         |
| `on_delete=models.SET_NULL`       | Встановлює `NULL`, якщо батьківський запис видалено                 |
| `related_name="..."`              | Додає зворотне посилання для доступу до об'єктів з іншої моделі   |
| `related_query_name="..."`        | Використовується у фільтрах для зворотних запитів                 |
| `through="..."`                 | Визначає власну проміжну таблицю для `ManyToManyField`            |

7. ***Висновок:***

 - `ForeignKey` - для **один-до-багатьох** (М:1)
 - `OneToOneField` - для **один-до-одного** (1:1)
 - `ManyToManyField` - для **багато-до-багатьох** (М:М)
 - `Self-referential relationships` - коли модель посилається сама на себе.

