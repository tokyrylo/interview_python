# Транзакції у Django та ACID 

***1. Що таке транзакції***
**Транзакція** - це группа операцій з базою даних, які виконуються як одне ціле. Якщо одна з операцій не виконується то всі інщі відміняються.
Django підтримує **транзакційність** завдяки ORM і дозволяє керувати транзакціями через `atomic`, `commit` та `rolback`.

***2. Властивості ACID у транзакціях***
Транзакції баз даних дотримуються **ACID-принципів**, що гарантує їхню надійність:
1. **Атомарність (Atomicity)**
 - Всі операції в транзакції виконуються або всі скасовуються.
 - Нариклад, якщо ви переказуєте гроші з одного рахунку на інший, обидва записи в БД мають оновитися одночасно.

2. **Цілісність (Consistency)**
 - База даних завжди залишається в конкретному стані.
 - Наприклад, якщо транзакція змінює баланс, то вона не повинна призводити до негативного балансу.

3. **Ізоляція (Isolation)**
 - Одна транзакція не повинна впливати на інші.
 - Django за замовченням використовує рівень ізоляції **READ COMMITED**.

4. **Надійність (Durability)**
 - Після завершення транзакції дані гарантовано зберігаються в базі, навіть якщо сервер раптово вимкнеться.

***3. Використання транзакціх у Django***

**3.1 Автоматичне управління транзакціями**
Django за замовченням виконує кожен запит у **своїй окремій транзакції** і автоматично **комітить** (`commit()`) її після виконання запиту.
Якщо в запиті сталася помилка, Django робить **відкат** (`rolback()`).

**3.2 Використання `atomic()`**
Django надає декоратор та контекстний менеджер `atomic()`, який дозволяє виконувати кілька операцій у мережах однієї транзакції.

**Приклад використання `atomic` як декоратора:**

```python 
from django.db import transaction
from django.shortcuts import get_object_or_404


@transaction.atomic
def transfer_money(sender_id, receiver_id, amount):
  sender = get_object_or_404(User, id=sender_id)
  receiver = get_object_or_404(User, id=receiver_id)

  if sender.balance < amount:
    raise ValueError("Not enough money!")
  
  sender.balance -= amount
  sender.save()

  receiver.balance += amount
  receiver.save()
```
Якщо станеться помилка (наприклад, `raise ValueError`), зміни не будуть збережені.

**Приклад використання `atomic` як контекстного менеджера**

```python 
from django.db import transaction

def create_user_with_profile():
  with transaction.atomic():
    user = User.objects.create(username="john_doe")
    Profile.objects.create(user=user, bio="Hello World")
```
Якщо створення профілю не вдасться, **користувач також не буде створений**.

**3.3 Управління `commit` та `rolback` в ручну**
Якщо вам потрібно **явно** керувати комітами та відкатами:

```python 
from django.db import transaction

def create_user_manually():
  try:
    with transaction.atomic():
      user = User.objects.create(username="john_doe")
      Profile.objects.create(user=user, bio="Hello World")
      transaction.commit() # Зберігаємо зміни 
  except Exception:
    transaction.rolback() # Відкочюємо зміни
```

**3.4 `select_for_update()` для блокування записів**
Якщо потрібно заблокувати запис у БД, щоб інші транзакції не могли його змінити:

```python 
from django.db import transaction

def update_balance(user_id, amount):
  with transaction.atomic():
    user = User.objects.select_for_update().get(id=user_id)
    user.balance -= amount 
    user.save()
```
Це захищає від **конкурентних змін**

***Висновки***

- **Транзакції** в Django допомагають гарантувати цілісність даних.
- **ACID** гарантує надійність операцій з базою даних.
- Використовуйте `@transaction.atomic()` для атомарних змін.
- `select_for_update()` запобігає конфліктам у конкурентному середовищі.
