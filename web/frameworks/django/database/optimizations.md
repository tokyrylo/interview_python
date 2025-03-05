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

***Висновок***

- `select_related()` - для **ForeignKey**, робить **JOIN** (1 SQL-запит).
- `prefetch_related()` - для **ManyToManyField**, робить **2 SQL-запити** та об'єднує в Python.
- Використовуйте обидва методи **для оптимізації запитів** та уникнення "N+1" проблеми.
