# Mutable types

У мові програмування `Python` є `mutable` типи даних, це змінні типи даних, що дозволяє змінювати їх значення без зміни адреси у пам'яті. Тобто, коли ми змінюємо їх значення, ми не створюємо новий об'єкт, а змінюємо його у тій області пам'яті.


## Мутабельні типи даних 

 - `list` 
 - `set` 
 - `dict` 
 - `bytearray`


***Приклад списків***:
```python
my_list = [1, 2, 3]
print(id(my_list)) # ID адреси в пам'яті

my_list.append(4) # Додаємо елемент у список 
print(my_list) # [1, 2, 3, 4]
print(id(my_list)) # ID той самий 
```

***Приклад словників***

```python
my_dict = {"a": 1, "b": 2}
print(id(my_dict))

my_dict["c"] = 3 
print(my_dict)
print(id(my_dict))
```

## Cons

 - Використання мутабельних типів за замовченням у функціях

```python
def add_item(item, my_list = []):
  my_list.append(item)
  return my_list

print(add_item(1)) # [1]
print(add_item(2)) # [1, 2] (а не два як можна було очикувати)
```
Список my_list не створюється наново при кожному виклику функції, а використовувається той самий об'єкт

***Привильний варіант***:

```python
def add_item(item, my_list=None):
  if my_list is None:
    my_list = []
  my_list.append(item)
  return my_list
```
***Як зробити копію мутабельного об'єкту***:

- Поверхнева копія(`shallow copy`)

```python
original = [1, 2, [3, 4]]
copy_list = original.copy() # або list(original)

copy_list[2].append(5)

print(original) # [1, 2, [3, 4, 5]]
print(copy_list) # [1, 2, [3, 4, 5]]
```
Так вкладений список змінеться у обох копіях


- Глибока копія(`deep copy`)

```python
import copy

original = [1, 2, [3, 4]]
deep_copy_list = copy.deepcopy(original)

deep_copy_list[2].append(5)

print(original) # [1, 2, [3, 4]]
print(deep_copy_list) # [1, 2, [3, 4, 5]]
```

## Висновки:

1. Мутабельні типи можна змінювати без створення нового об'єкт
2. Зміни в мутабельному об'єкті можуть впливати на інші зміни, що посилаються на нього.
3. Треба бути обережним зі значеннями за замовченням у функціях.
4. Користуватися `copy.copy()` або `copy.deepcopy()`, якщо потрібно створити новий об'єкт.
