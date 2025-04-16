# Посередник (Mediator)

## Суть патерна

**Посередник (Mediator)** - це повідінковий патерн, який дозволяє об'єктам взаємодіяти опесередковано через окремий об'єкт-посередник, замість того, щоб взаємодіяти напряму один з одним.
Це допомогає зменшити кількість залежностей між об'єктами та спростити їхню комунікацію.

## Проблема

У складних системах компоненти часто взаэмодыють мыж собою. Якщо кожен компонент знає про інші - виникає **павутиння зв'язків**, яке складно підтримувати.
Наприклад: кнопка, текстове поле, чекбокс - і всі вони якось реагують один на одного.

## Рішення

Винести всю логіку взаємодії між компонентами в окремий клас - **посередник**. Компоненти більше не знають один про одного, вони просто "повідомляють" посередника про свої дії.

## Приклад

```python
from abc import ABC, abstractmethod

# Абстрактний посередник
class Mediator(ABC):
    @abstractmethod
    def notify(self, sender: object, event: str):
        pass

# Базовий компонент
class BaseComponent:
    def __init__(self, mediator: Mediator = None):
        self._mediator = mediator

    def set_mediator(self, mediator: Mediator):
        self._mediator = mediator

# Конкретні компоненти
class Button(BaseComponent):
    def click(self):
        print("Кнопка натиснута")
        self._mediator.notify(self, "click")


class TextBox(BaseComponent):
    def __init__(self):
        super().__init__()
        self.enabled = False

    def enabled(self):
        self.enabled = True
        print("Поле вводу активоване")

    def disable(self):
        self.enable = False
        print("Поле вводу деактивоване")

class CheckBox(BaseComponent):
    def __init__(self):
        super().__init__()
        self.checked = False

    def toggle(self):
        self.checked = not self.checked
        print(f"Чекбокс {'увімкнено' if self.checked else 'вимкнено'}")
        self._mediator.notify(self, "toggle")

# Конкретний посередник
class UIFormMediator(BaseComponent):
    def __init__(self, button: Button, textbox: TextBox, checkbox: CheckBox):
        self._button = button
        self._textbox = textbox
        self._checkbox = checkbox

        self._button.set_mediator(self)
        self._textbox.set_mediator(self)
        self._checkbox.set_mediator(self)

    def notify(self, sender: object, event: str):
        if sender == self._checkbox and event == "toggle":
            if self._checkbox.checked:
                self._textbox.enable()
            else:
                self._textbox.disable()

# Використання
button = Button()
textbox = TextBox()
checkbox = CheckBox()

mediator = UIFormMediator(button, textbox, checkbox)

checkbox.toggle() # Чекбокс увімкнено 
checkbox.toggle() # Чекбокс вимкнено
```

## Коли використовувати
 - Коли багато об'єктів мають складну логіку взаємодії.
 - Коли потрібно уникнути "злипання" компонентів (щоб вони не знали один про одного).
 - Коли часто змініються логіка взаємодії між об'єктами.

## Переваги
 - Зменьшує кількість залежностей між компонентами.
 - Централізує логіку комунікації.
 - Полегшує зміну поведінки без модифікації самих компонентів.

## Недоліки
 - Посередник може стати **великим і складним**, якщо логіки дуже багато.
 - Централізація = один "**вузький**" компонент, який може стати точкою відмови.

## Аналогія з життя

Уяви собі **диспетчера в аєропорту**. Пілоти не спілкуються напряму між собою. Вони передають всю інформацію диспетчеру, а той вже координує їхні дії.

## Типові сценарії

 - Комунікація між UI-елементами (вікна, поля, кнопки).
 - Координація між модулями в складних системах.
 - Взаємодія між об'єктами, які повинні залишатися слабо пов'язаними.

 