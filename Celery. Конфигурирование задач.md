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

 
 
 
#####  **Маршрутизация** https://django.fun/docs/celery/5.1/userguide/routing/#automatic-routing

1. В качестве альтернативы можно использовать сопоставление шаблонов glob или даже регулярные выражения для сопоставления всех задач в пространстве имен `feed.tasks`
```python
app.conf.task_routes = {'feed.tasks.*': {'queue': 'feeds'}}
```
Если порядок соответствия шаблонов важен, то вместо этого следует указать маршрутизатор в формате _items_:
```python
task_routes = ([
    ('feed.tasks.*', {'queue': 'feeds'}),
    ('web.tasks.*', {'queue': 'web'}),
    (re.compile(r'(video|image)\.tasks\..*'), {'queue': 'media'}),
],)
```
Или указывать очередь прямо в момент создания задачки
```python
@celery_app.task(name='gateway.tasks.covid_sms_send', queue='sms-channel')
```

После установки маршрутизатора вы можете запустить сервер z для обработки только очереди фидов следующим образом
```python
celery -A proj worker -Q feeds
```

**Разбор команды из докер композа**
```python
celery worker -A modules.officialsnotification -l INFO -c 1 -Q push-channel -n push_worker
```
- `celery worker`: Эта команда используется для запуска Celery worker.
- `-A modules.officialsnotification`: Опция `-A` указывает на имя (или путь) модуля, где находится объект Celery (в данном случае `officialsnotification` из пакета `modules`).
- `-l INFO`: Устанавливает уровень журналирования на INFO, что определяет, какие сообщения будут отображаться в выводе. INFO будет отображать информацию о выполненных задачах.
- `-c 1`: Устанавливает количество процессов для обработки задач в воркере. В данном случае установлено 1 процесс.
- `-Q push-channel`: Указывает, какая очередь будет обрабатываться этим воркером. В данном случае установлена очередь с именем `push-channel`.
- `-n push_worker`: Устанавливает имя воркера. В данном случае имя воркера установлено как `push_worker`.