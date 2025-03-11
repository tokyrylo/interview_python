# Prototype (Прототип) в Python 

**Prototype (Прототип)** - це породжувальний патерн, який дозволяє **створювати нові об'єкти на основі вже існуючих, копіюючи їх**.

**Навіщо потрібен Prototype**

- Уникнення **дорогих операцій ініціалізації** нового об'єкту.
- Створення **нових екземплярів без виклику** нового об'єкту.
- Гнучке створення **копій складних об'єктів**.

***Класичний приклад: Клонування автомобілів***

Уявимо, що створення об'єкта `Car` займає багато ресурсів. Використовуємо **Prototype**, щоб швидко копіювати екземпляри. 

1. **Базовий клас**

```python 
import copy 

class Car:
  def __init__(self, model, engine, color):
    self.model = model
    self.engine = engine
    self.color = color

  def clone(self):
    return copy.deepcopy(self)

  def __str__(self):
    return f"Car(model={self.model}, engine={self.engine}, color={self.color})"
```

2. **Використання Prototype**

```python 
# Оригинальний об'єкт 
original_car = Car("Tesla Model S", "Electric", "Red")
print("Original:", original_car)

# Створюємо копію 
cloned_car = original_car.clone()
cloned_car.color = "Red" # Змінюємо колір у копії 

print("Cloned:", original_car) # Car(model=Tesla Model S, engine=Electric, color=Blue)
print("Original (unchanged):", original_car) # Car(models=Tesla Model S, engine=Electric, color=Blue)
```

***Де використовується Prototype у реальному коді?***
- `copy.copy()` та `copy.deepcopy()` - стандартний механізм клонування в Python.
- `Django ORM` - клонування об'єктів моделей перед їх зміною.
- `Flask request context` - створення копій для окремих потоків.

***Коли варто використовувати Prototype?***
- Якщо створення нового об'єкта через **конструктор занадто довго**.
- Якщо об'єкт **має багато вкладених структур**, які потрібно глибко копіювати.
- Якщо потрібно **уникати складної логіки ініціалізації** для кожного нового екземпляра.


