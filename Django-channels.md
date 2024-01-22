Библиотека позволяет выполнять соединение с клиентской стороной как через http, так и через websocket. Подключая библиотеку к Django соединения будут выполняться через сервер-стандарт asgi, так как библиотека переопределит стандартное поведение Django. В остальном же функциональность остаётся та же самая, как обычно, через http можно вызывать контроллеры из файла views.py.

## **Классы-Потребители (Consumers)**

Это специальный класс — потребитель сообщений. Принято располагать эти классы в модуле consumers.py.

Эти классы обрабатывают сообщения по протоколам отличным от http, например для работы с websocket класс создаётся следующим образом:
```python
from channels.generic.websocket import WebsocketConsumer

class SimpleWsConsumer(WebsocketConsumer):
    pass
```
В отличие от стандартной конфигурации при работе через http, когда маршруты располагаются в модуле urls.py при работе через websocket маршруты принято располагать в модуле routing.py > [!check] 


```python
from django.urls import re_path
from . import consumers

websocket_urlpatterns=[
    re_path(r’ws/chat/(?P<room_name>\w+)/$’, consumers.SimpleWsConsumer.as_asgi()),
]
```
