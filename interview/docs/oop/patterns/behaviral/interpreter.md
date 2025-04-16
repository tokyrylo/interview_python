# Інтерпретатор (Interpreter)

## Суть патерна

**Інтерпретатор** - це поведінковий патерн, який визначає граматику мови та інтерпретує вирази цієї мови. Він дозволяє створювати власні мови або реалізовувати інтерпретатори для існуючих мов, де кожен вираз представлений окремим об'єктом.

## Проблема

Припустимо, у вас є простенька мова або DSL (Domain-Specific Language), наприклад:

`"1 + 2 - 3 + 4"`.

Як її зрозуміти й обчислити? Писати ручний парсер кожного разу незручно. Патерн **Інтерпритатор** дозволяє створити набір класів, які розуміють цю мову та можуть її виконати.

## Рішення

Створюємо абстрактний клас `Expression`, а потім - конкретні вирази: `Number`, `Add`, `Substract`, які реалізують метод `interpret(context)`.


## Приклад реалізації на Python

```python
from abc import ABC, abstractmethod

# Абстрактний вираз
class Expression(ABC):
    @abstractmethod
    def interpret(self) -> int:
        pass

# Конкретні вирази
class Number(Expression):
    def __init__(self, value: int):
        self.value = value

    def interpret(self) -> int:
        return self.value


class Add(Expression):
    def __init__(self, left: Expression, right: Expression):
        self.left = left
        self.right = right

    def interpret(self) -> int:
        return self.left.interpret() + self.right.interpret()

class Substract(Expression):
    def __init__(self, left: Expression, right: Expression):
        self.left = left
        self.right = right

    def interpret(self) -> int:
        return self.left.interpret() - self.right.interpret()

# Побудова дерева виразів: (1 + 2) - (3 + 4)
expr = Substract(
    Add(Number(1), Number(2)),
    Add(Number(3), Number(4)),
)

print(expr.interpret()) # Виведе; -4 
```

## Коли використовувати

 - Коли потрібно реалізувати просту мову або DSL.
 - Коли мова має **чітку граматику**, яку можна виразити у вигляді абстрактного синтаксичного дерева.
 - Коли **структура виразів стабільна**, але ви хочете мати гнучкість в інтерпретації.

## Переваги

 - Простота розширення мови (додавання нових виразів).
 - Весь синтаксис мови інкапсульований у відповідних класах.
 - Гарно працює з деревоподібними структурами виразів.

## Аналогія з життя

Уяви, що ти створюєш калькулятор, який не просто виконує `eval()`, а будує дерево:
"2 + 2" = Add(Number(2), Number(2)) -> потім виконується.

## Типове використання

 - DSL у бізнес-логіці: наприклад, "IF клієнт.gold AND замовлення > 5000 THEN знижка".
 - Прості математичні або логічні мови.
 - Інтерпритатори команд для ігор, скриптів, інструкцій.