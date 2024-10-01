Замечание: 
`"Вынеси запуск в отдельный sh файл (и вызывай в docker-compose > service > entrypoint), либо сразу добавляй менеджер процессов в запуск. runserver - только для разработки"`

Объяснение:
- **"Вынеси запуск в отдельный sh файл"**: Это означает, что нужно создать отдельный скрипт (например, `entrypoint.sh`), который будет запускать сервер. Это лучше с точки зрения модульности и поддержки, так как вам проще будет изменять процесс запуска и добавлять новые команды при необходимости.
    
- **"И вызывай в docker-compose > service > entrypoint"**: Вместо того, чтобы указывать команду в `CMD`, лучше воспользоваться директивой `entrypoint` в `docker-compose.yml`, которая будет вызывать созданный `entrypoint.sh`.
    
- **"Либо сразу добавляй менеджер процессов в запуск"**: `python manage.py runserver` не рекомендуется использовать в production, так как это встроенный development-сервер Django, который не оптимизирован для производственного окружения. В production нужно использовать менеджеры процессов (например, `gunicorn` или `supervisord`), которые запускают приложение с поддержкой многопоточности и другими улучшениями.

1. При создании докер файла лучше выносить команду запуска в отдельный файл entrypoint.sh
```
Dockerfile
...
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
----
Заменить на 
ENTRYPOINT ["./entrypoint.sh"]
```

2. В папку с Dockerfile создать файл entrypoint.sh

3. Запускать лучше через Gunicorn, так как manage.py только для теста
```
#!/bin/bash

echo "Apply database migrations"
python manage.py migrate

echo "Collect static files"
python manage.py collectstatic --noinput

echo "Starting Gunicorn"
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000 --workers 4

```

4. Установите `gunicorn` в `requirements.txt`
5. Обновить docker-compose.yml
```
services:
  web:
    build: .
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    entrypoint: ["./entrypoint.sh"]
```
