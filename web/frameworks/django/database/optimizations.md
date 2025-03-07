# Оптимізація запросів
***`select_related()` та `prefetch_related()` у Django ORM***

Обидва методи(`select_related()` та `prefetch_related()`) використовуються для **оптимізації запитів** при роботі з **ForeignKey** та **ManyToMany** зв'язками. Вони допомогають уникнути **"N+1" проблеми** та зменшують кількість SQL-записів.

***1. `select_related()`***
Цей метод **"жадібно"** завантажує дані з пов'язаних моделей **одним JOIN-запитом**. Він використовується тільки для **ForeignKey** i **OneToOneField**.

```python
from myapp.models import Post

posts = Post.objects.select_related("author").all()
```
- **Що відбувається?**:
  - Django зробить **один SQL-запит** з **JSON** між таблицями `Post` та `User`.
  - Доступ до `post.author.email` не створить додаткового запиту.
  - **SQL-запит, який буде виконано:**
  
  ```sql
  SELECT post.*, user.*
  FROM post
  JOIN user ON post.author_id = user.id;
  ```
Використовуйте `select_related()`, коли потрібно **уникнути додаткових запитів** при доступі до пов'язаних об'єктів `ForeignKey`.


***2. `prefetch_related()`***

Цей метод завантажує дані **окремими запитами**, а потім об'єднує їх на рівні Python. Використовується для **ManyToManyField** та випадків, коли `select_related()` неможливий.

**Приклад використання `prefetch_related()`**

```python
from myapp.models import Post

posts = Post.objects.prefetch_related("comments").all()
```
- **Що відбувається?**

  - Django виконає **два SQL-запити** (один для `Post`, другий для `Comment`).
  - Потім ORM автоматично прив'яже `comments` до відповідних `Post`.
  - **SQL-запити, які будуть виконані:**

  ```sql
  SELECT * FROM post;
  SELECT * FROM comment WHERE post_id IN (1, 2, 3, ...);
  ```
Використовуйте `prefetch_related()` для **ManyToManyField** або коли Django не може зробити `JOIN`.

***Порівняння `select_related` та `prefetch_related`***

| Функція | `select_related` | `prefetch_related` |
| --------------- | --------------- | --------------- |
| **Як працює?** | Використовує `JOIN` | Окремі запити + обробка в Python |
| **Які зв'язки** | `ForeignKey`, `OneToOneField` | `ManyToManyField`, `Reverse ForeignKey` |
| **Кількість запитів SQL** | 1 | 2+ |
| **Продуктивність** | Швидше для 1:1 і `ForeignKey` | Швидше для M2M і багатьох об'єктів |


***Комбіноване використання***

Іноді краще використовувати **обидва методи разом:**

```python
posts = Post.objects.select_related("author").prefetch_related("comments").all()
```
Django зробить `JOIN` для `author` і виконає окремий запит для `comments`.

***3. Використання `only()` та `defer()`***

Якщо потрібно **отримати тільки певні поля**, варто уникати вибірки зайвих даних 

```python
User.objects.only("id", "username") # Завантаження лише  ці поля 
User.objects.defer("big_text_field") # Відкладає завантаження валикого поля 
```

***4. Використання `values()` та `values_list()`***
Замість вибірки об'єктів можна отримувати **лише необхідні дані** у вигляді словників або кортежів.

```python 
# Повертає список словників 
User.objects.values("id", "username")

# Повертає список кортежів 
User.objects.values_list("id", "username", named=True)
```

**5. Використання `exists()` замість `count()`**
Якщо потрібно перевірити,  чи є записи, `exists()` значно швидше, ніж `count()`

```python 
if User.objects.filter(email="test@example.com").exists():
  print("User exists !")
```
**Не робіть так**: `if User.objects.filter(email="test@example.com").count() > 0:` - це повільно 

***6. Використання `bulk_create()` та `bulk_update()`***
Замість циклу `save()` для кожного об'єкта:

```python 
users = [User(username=f"user{i}") for i in range(1000)]
User.objects.bulk_create(users) # Створює 100 записів одним запитами 
```

**ПОГАНО**

```python 
for user in users:
  user.save() # 1000 окремих SQL-запитів (дуже повільно)
```


***Висновок***

- `select_related()` - для **ForeignKey**, робить **JOIN** (1 SQL-запит).
- `prefetch_related()` - для **ManyToManyField**, робить **2 SQL-запити** та об'єднує в Python.
- Використовуйте обидва методи **для оптимізації запитів** та уникнення "N+1" проблеми.
