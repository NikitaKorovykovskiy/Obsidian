https://proglib.io/p/django-celery-i-redis-gayd-po-rabote-s-asinhronnymi-zadachami-2022-08-22
Celery предоставляет несколько способов для конфигурирования задач. Вот некоторые из них:

1. **Файл конфигурации (Celery Configuration File):** Вы можете создать файл конфигурации (например, `celeryconfig.py`) и использовать его для настройки параметров задач.
	```python
	# celeryconfig.py
	broker_url = 'pyamqp://guest:guest@localhost//'
	result_backend = 'rpc://'
	task_serializer = 'json'

```
	И затем указать путь к файлу при запуске Celery:
	```python
	celery -A your_celery_app worker --config=celeryconfig
```

2. **Передача параметров при запуске (Command Line):** Вы можете передавать параметры конфигурации при запуске Celery напрямую через командную строку:
```python
celery -A your_celery_app worker --broker=pyamqp://guest:guest@localhost//
```
3. **Программное конфигурирование (Programmatic Configuration):** Вы также можете конфигурировать Celery непосредственно в коде приложения:
	```python
from celery import Celery app = Celery('your_celery_app') app.config_from_object('celeryconfig')
```
	В этом случае, вы можете использовать объект Python (например, класс или модуль), который содержит настройки задач.

4. **Использование переменных окружения (Environment Variables):** Некоторые параметры могут быть установлены через переменные окружения. Например, для установки брокера через переменную окружения:
	```python
	export CELERY_BROKER_URL=pyamqp://guest:guest@localhost// celery -A your_celery_app worker
```
	В этом случае, параметры Celery будут читаться из переменных окружения.