Тестовый проект git@github.com:django/channels.git
Библиотека позволяет выполнять соединение с клиентской стороной как через http, так и через websocket. Подключая библиотеку к Django соединения будут выполняться через сервер-стандарт asgi, так как библиотека переопределит стандартное поведение Django. В остальном же функциональность остаётся та же самая, как обычно, через http можно вызывать контроллеры из файла views.py.

## **Классы-Потребители (Consumers)**

Это специальный класс — потребитель сообщений. Принято располагать эти классы в модуле consumers.py.

Эти классы обрабатывают сообщения по протоколам отличным от http, например для работы с websocket класс создаётся следующим образом:
```python
from channels.generic.websocket import WebsocketConsumer

class SimpleWsConsumer(WebsocketConsumer):
    pass
```
В отличие от стандартной конфигурации при работе через http, когда маршруты располагаются в модуле urls.py при работе через websocket маршруты принято располагать в модуле ==routing.py==
```python
from django.urls import re_path
from . import consumers

websocket_urlpatterns=[
    re_path(r’ws/chat/(?P<room_name>\w+)/$’, consumers.SimpleWsConsumer.as_asgi()), <= Тут
]
```
Как видим, у класса-контроллера вместо обычного вызова as_view() вызывается метод ==as_asgi()==, в остальном модуль маршрутов похож на стандартный urls.py

Подключаются же такие маршруты в модуле asgi.py
```python
import os

from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.security.websocket import AllowedHostsOriginValidator
from django.core.asgi import get_asgi_application
from chat import routing # Импортируется модуль routing.py, где как раз и настроены маршруты websocket

os.environ.setdefault('DJANGO_SETTINGS_MODULE','mysite.settings')
application=ProtocolTypeRouter({
    "http":get_asgi_application(),
    "websocket":AllowedHostsOriginValidator(AuthMiddlewareStack(URLRouter(routing.websockets_urlpatterns)) # Передаётся список маршрутов из модуля routing.py)
})
```

## **Подключение коммуникационной системы потребителей. Слой Каналов (Channel layer)**

Канал — это как путь, через который направляются сообщения. Для каждой вкладки веб-обозревателя создаётся свой канал. Каждому потребителю по умолчанию назначается уникальное имя канала channel_name, то есть если создать 10 вкладок (или приложение используют 10 разных пользователей), то будет создано 10 каналов. Наверное можно думать о канале как о канале соединения websocket. Каналы могут объединяться в группы, и тогда каналы, принадлежащие одной группе могут общаться между собой

Группа — это группа связанных каланов. Имея имя группы можно отправлять сообщения через все каналы этой группы. И, насколько понимаю, для каждого потребителя создаётся свой собственный канал с уникальным именем. Получается, что каналы объединяются в группы вместе со своими потребителями.

То есть можно сказать, что канал — это путь к потребителю, по которому идут сообщения, а потребитель — конечная точка для этих сообщений. Потребитель может принять сообщение, обработать его и отправить ответ.

Коммуникационная система работает через redis — нужно запустить redis и добавить пакет channels_redis

pip install channels_redis

И в модуле настроек settings.py коммуникационную систему нужно сконфигурировать
```python
ASGI_APPLICATION='mysite.asgi.application'

CHANNEL_LAYERS={
    'default':{
        'BACKEND':'channels_redis.core.RedisChannelLayer',
        'CONFIG':{
            'hosts':[
                ('127.0.0.1',6379)
            ]
        }
    }
}
```

### **Добавление потребителя в группу**

Потребителя можно добавить в группу методом channel_layer.group_add  

```python
import json

from asgiref
.sync import async_to_sync
from channels.generic.websocket import WebsocketConsumer

class SimpleWsConsumer(WebsocketConsumer):
    def connect(self):
        self.room_name=self.scope['url_route'][^1]['kwargs']['room_name']
        self.room_group_name='chat_%s' % self.room_name
        # Добавление потребителя в группу
        async_to_sync(self.channel_layer.group_add)[^2](
            self.room_group_name, self.channel_name # Параметры метода channel_layer.group_add
        )
    self.accept() # Принять соединение
```
