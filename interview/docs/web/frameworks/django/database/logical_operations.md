# Логічні операції у фільтрах

**Q - Логічні операції у фільтрах**, дозволяє використовувати логічні оператори (`AND`, `OR`, `NOT`) у запитах.
За замовченням `filter()` використовується логічне `AND`, але `Q` дозволяє писати складніші запити, використовуючи `OR` (`|`), `AND` (`&`), та `NOT` (`~`).

***Приклади `Q`***

1. Використання `AND` (`&`)

```python
from django.db.models import Q 
from myapp.models import Post 


# Отримати всі пости, де автор "John" і статус "published"
posts = Post.objects.filter(Q(author="John") & Q(status="published"))
```

2. Використання `OR` (`|`)

```python
posts = Post.objects.filter(Q(author="John") | Q(status="published"))
```
Поверне всі пости, де **або** автор "John", **або** статус "published"

3. Використання `NOT` (`~`)

```python
posts = Post.objects.filter(~Q(status="draft"))
```
Поверне всі пости, де статус **не** "draft"

4. Комбіновані умови

```python
posts = Post.objects.filter(Q(author="John") & (~Q(status="draft") | Q(is_featured=True)))
```
Поверне пости, де **автор "John" і (статус не "draft" або `is_featured=True`)**

***`F` - Порівняння між полями***

`F` використовується, коли потрібно порівняти значення **двох полів в одному записі**.

**Приклади `F`**

1. **Фільтр, де одне поле більше за інше**

```python
from django.db.models import F 
from myapp.models import Order 


# Отримати замовлення, де відвантажено менше товарів, ніж замовленно.
orders = Order.objects.filter(shipped_quantity__lt=F("ordered_quantity"))
```

2. **Оновлення запису на основі іншого поля**

```python
Post.objects.update(views=F("views") + 1)
```
**Збільшує** `views` **на 1** для всіх записів.

3. **Фільтр з математичними операціями**

```python
products = Product.objects.filter(price__gt=F("discounted_price") * 1.1)
```
Поверне продукти, у яких ціна більше за `discounted_price` на 10%.

***Комбінування `Q` та `F`***

```python
Post.objects.filter(Q(views__gt=F("likes") * 2) | Q(comments__gte=10))
```
**Отримати всі пости**, де **переглядів удвічі більше за лайки** або **коментарів >= 10**.

***Висновок***

- `Q` - для **складних фільтрів** з `AND`, `OR`, `NOT`.
- `F` - для **порівняння полів** в одному записі.
- `Q` i `F` можна **комбінувати** для складних умов.


