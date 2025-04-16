# Стратегія (Strategy)

## Суть патерну

Патерн **Стратегія** дозволяє визначити сімейство алгоритмів, інкапсолювати кожен з них та робити їх взаємозамінними. Замість того, щоб мати багато умов `if-else`, можна делегувати вибір поведінки окремим об'єктам.

## Проблема

У вас э клас, який виконує різні операції в залежності від конфігурації. З часом `if` - `else` блоки стають громіздкими і важко підтримуються.

## Рішення

Виділити змінювану поведінку в окремому класі.

## Приклад 

```python
from abc import ABC, abstractmethod

# Інтерфейс стратегії
class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data):
        pass

# Конкретні стратегії
class QuickSortStrategy(SortStrategy):
    def sort(self, data):
        print("Сортуємо за допомогою QuickSort")
        return sorted(data) # Умовно

class BubleSortStrategy(SortStrategy):
    def sort(self, data):
        print("Сортуємо за допомогою BubleSortStrategy")
        return sorted(data) # Умовно

# Контекст
class Sorter:
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: SortStrategy):
        self._strategy = strategy

    def sort(self, data):
        return self._strategy.sort(data)

# Використання
data = [3, 1, 2]

sorter = Sorter(QuickSortStrategy())
sorter.sort(data)

sorter.set_strategy(BubleSortStrategy)
sorter.sort(data)
```

## Коли використовується

 - Коли є багато варіації алгоритмів, які можна взаємозаміняти.
 - Коли хочете уникнути великого `if-else` або `switch`.

## Переваги

 - Спрощує підтримку та тестування.
 - Можна легко додавати нові стратегії без змін у клієнтському коді.

## Недоліки

 - Кількість класів зростає.
 - Неочевидно для новачків, де стратегія використовується.