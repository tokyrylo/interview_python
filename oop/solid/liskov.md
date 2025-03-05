# Liskov Substitution Principle

***Принцип підстановки Лісков*** - об'єкти підкласів повинні замінюватися замість об'єктів базового класу без зміни коректності програми.

**Що це означає?**

- Якщо є клас наприклад `Parent`, то його підкласи `Child1`, `Child2` тощо **повинні мати можливість заміняти батьківський клас** у будь-якому місці коду.
- При цьому логіка программи не повинна ломатися.

Простими словами, якщо у тебе є код, який працює з `Parent`, він **повинен працювати так само і з** `Child`.

**Приклад порушення**:

```python

class Bird:
  def fly(self):
    return "I`m fly"
  

class Sparrow(Bird):
  pass 


class Penguin(Bird):
  def fly(self):
    raise Exception("Penguins cant fly")
```
**Проблема в цьому коді:**

- Ми створили `Penguin`, але він не може літати.
- Якщо наш код очикує, що всі `Bird` можуть літати, **виклик** `penguin.fly()` **спричинить помилку**.
- Отже, `Penguin` **не може повноцінно замінити** `Bird`, а це **порушує LSP**.

**Як виправити?**

```python
from abc import ABC, abstractmethod


class Bird(ABC):
  def __init__(self, name):
    self.name = name


class FlyingBird(Bird):
  def fly(self):
    return f"{self.name} is flying!"


class NonFlyingBird(Bird):
  def walk(self):
    return f"{self.name} is walking!"


class Sparrow(FlyingBird):
  pass 


class Penguin(NonFlyingBird):
  pass


sparrow = Sparrow("Sparrow")
penguin = Penguin("Penguin")

print(sparrow.fly()) # "Sparrow is flying!"
print(penguin.walk()) # "Penguin is walking!"
```

**Що змінилось?**

- Тепер **немає безглуздого методу** `fly()` у `Penguin`
- Всі класи **логічно организовані**: літаючи тварини мають `fly()`, а ті, що не літають - `walk()`
- Жоден **підклас не ламає логіку**

**Висновок, LSP допомогає:**

- **Унікати підкласів, які порушують очикувану поведінку**
- **Будувати стабільні та логічні ієрархії класів**
- **Мінізувати помилки через невідповідність підкласів батьківського класу**


