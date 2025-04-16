# Mixins 

**Mixins** - це спосіб повторного використання коду у `Django`, особливо у **Class-Based Views (CBV)** та **Django REST Framework (RDF)**. Вони дозволяють додавати функціональність до представлень, не дублюючи код.

***Навіщо потрібні Mixins:***
Коли ми використовуємо **CBV**, нам часто потрібно повторювати однаковий код у різних місцях. Щоб уникнути цього, Django дозволяє використовувати **mixins**, які розділяють логіку на багаторазові компоненти.

**Переваги mixins:**

- **Повторне використання коду**
- **Гнучка архітектура**
- **Спрощення підтримки коду**
- **Чітке розділення логіки**


***Приклад mixin у Django:***

Припустимо, що у нас є  кілька представлень, де потрібно перевіряти, чи є користувач власником об'єкта.

**Створимо `OwnerRequireMixin`:**

```python
from django.http import HttpResponseForbidden


class OwnerRequireMixin:
  def  dispatch(self, request, *args, **kwargs):
    obj = self.get_response()
    if obj.owner != request.user:
      return HttpResponseForbidden("Ви не є власником цього об'єкту")
    return super().dispatch(request, *args, **kwargs)
```

**Використання mixin у представленні:**

```python
from django.views.generic import DetailView
from .models import Post


class PostDetailView(OwnerRequireMixin, DetailView):
  model = Post
  template_name = "post_detail.html"
```
- Тепер, якщо користувач не є власником поста, він отримає **403 Forbidden**

***Mixins у Django REST Framework (DRF):***

Django REST Framework (DRF) містить **готові mixins**, які спрощують створення API.
**Приклади використання у DRF:**

```python
from rest_framework import mixins, generics 

from .models import Post
from .serializers import PostSerializer


class PostListCreateView(
  mixins.ListModelMixin,
  mixins.CreateModelMixin,
  generics.GenericAPIView
):
  queryset = Post.objects.all()
  serializer_class = PostSerializer

  def get(self, request, *args, **kwargs):
    return self.list(request, *args, **kwargs)

  def post(self, request, *args, **kwargs):
    return self.create(request, *args, **kwargs)
```

**Що робить цей клас?**
- `ListModelMixin` -> Отримує список об'єктів (`GET /posts/`)
- `CreateModelMixin` -> Створює об'єкт (`POST /posts/`)

Код можна зробити ще коротшим, використовуючи **generic views**:

```python
from rest_framework.generics import ListCreateAPIView 


class PostListCreateView(ListCreateAPIView):
  queryset = Post.objects.all()
  serializer_class = PostSerializer
```

***Вбудовані mixins у Django REST Framework:***

| Mixin | Опис |
| -------------- | --------------- |
| `CreateModelMixin` | Створення об'єкта (`POST`) |
| `ListModelMixin` | Отримання списку (`GET`) |
| `RetrieveModelMixin` | Отримання одного об'єкта (`GET /id/`) |
| `UpdateModelMixin` | Оновлення об'єкта (`PUT`) |
| `DestroyModelMixin` | Видалення об'єкта (`DELETE`) |

***Висновок***

- **Mixin** - це спосіб додавання функціоналу до CBV без дублювання коду.
- У **Django** mixins використовуються для перевірки прав, обробки запитів та інших завдань.
- У **Django REST Framework** вже є **готові mixins** для API-запитів.
