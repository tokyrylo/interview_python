# Django forms 

**Форми** в Django використовуються для обробки введених користувачем даних. Django надає вбудовону систему форм, яка дозволяє легко створювати, перевіряти та обробляти дані в запитах (наприклад, у формах HTML).
Основні властивості Django Forms:

 - Генерація HTML-форм автоматично.
 - Вбудована валідація (перевірка коректності даних).
 - Захист від CSRF-атак.
 - Легка інтеграція з моделями бази даних.

***Основний клас Django Forms***

Всі форми в Django успадковуються від `django.forms.Form` або `django.forms.ModelForm`.
Ось приклад просту форму `Form`:

```python 
from django import forms 


class ContactForm(forms.Form):
  name = forms.CharField(max_length=100)
  email = forms.EmailField()
  message = forms.CharField(widget=forms.Textarea)

# Використання у views.py 

from django.shortcuts import render 

from .forms import ContactForm


def contact_view(request):
  if request.method == "POST":
    form = ContactForm(request.POST)
    if form.is_valid():
      # Обробка даних (збереження, відправка email тощо)
      print(form.cleaned_data)
  else:
    form = ContactForm()

  return render(request, "contact.html", {"form": form})
```
У цьому прикладі `form.cleaned_data` містить перевірені дані, які користувач ввів у форму.

***Різниця `ModelForm` та просто `Form`***

| Функція | Form | ModelForm |
| --------------- | --------------- | --------------- |
| Використовується для | Будь-яких даних (не обов'зково з моделі) | Даних, пов'язаних з моделю |
| Потрібно вручну задавати поля? | Так | Ні, поля генеруються на основі моделі |
| Чи потрібно зберігати вручну? | Так | Ні, є вбудований метод `.save()` |
| Код простіший? | Ні | Так, бо автоматично працює з моделями |

***Приклад `ModelForm`***

```python
from django import forms
from .models import Contact 


class ContactForm(forms.ModelForm):
  class Meta:
    model = Contact # Вказуємо модель 
    fields = [
      "name", 
      "email", 
      "message",
    ] # Поля, які будуть у формі 
``` 

Цей код створить автоматично форму з тими ж полями, що й у моделі `Contact`. **Головна перевага:** нам не потрібно вручну вказувати поля форми.

***Висновок:***

 - `Form` використовується для кастомних форм, які не прив'язані до моделі.
 - `ModelForm` автоматично створює форму на основі моделі.
