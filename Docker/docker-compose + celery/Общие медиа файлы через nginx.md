Чтобы решить проблему с доступом к картинкам между микросервисами, запущенными в одном Docker Compose, можно использовать следующие подходы:

### 1. **Общие тома (Shared Volumes)**

Если оба микросервиса находятся в одном Docker Compose, они могут получить доступ к одной и той же файловой системе через общий том. В этом случае вы можете сделать папку `media` доступной для обоих контейнеров через том в `docker-compose.yml`.

Пример конфигурации `docker-compose.yml` с общим томом для папки `media`:

yaml

Копировать код

`version: '3' services:   scraper_service:     image: scraper_service_image     volumes:       - ./media:/app/media  # Общий том для папки media     ports:       - "8000:8000"    ml_model_service:     image: ml_model_service_image     volumes:       - ./media:/app/media  # Тот же общий том     ports:       - "8001:8001"  volumes:   media:`

Теперь обе службы могут видеть и читать файлы в папке `media`.

#### Пример передачи URL:

Когда вы передаёте URL в POST-запросе, вы можете использовать локальный путь внутри контейнера (например, `/app/media/scraper/product/images/your_image.jpg`), так как оба контейнера имеют доступ к одной и той же папке.

### 2. **Использование Nginx для раздачи статических файлов**

Если вы хотите передавать полный URL на изображение (например, через HTTP), вам нужно убедиться, что ваш сервис может отдавать файлы из папки `media` через HTTP.

Для этого можно настроить Nginx или другой HTTP-сервер, чтобы он обслуживал файлы из папки `media`.

Пример конфигурации для Nginx в `docker-compose.yml`:

yaml

Копировать код

`version: '3' services:   nginx:     image: nginx     volumes:       - ./media:/usr/share/nginx/html/media  # Путь к папке media на хосте     ports:       - "80:80"     depends_on:       - scraper_service    scraper_service:     image: scraper_service_image     volumes:       - ./media:/app/media  # Путь к media в контейнере    ml_model_service:     image: ml_model_service_image     volumes:       - ./media:/app/media     ports:       - "8001:8001"`

С такой настройкой файлы из папки `media` будут доступны по URL:

ruby

Копировать код

`http://<your-host>/media/scraper/product/images/your_image.jpg`

### 3. **Исправление URL для передачи**

Если ваш `ml_model_service` пытается загрузить изображение по URL, который выглядит как `http://127.0.0.1/media/…`, нужно учитывать, что внутри Docker 127.0.0.1 относится только к контейнеру, а не к хосту. Вместо этого необходимо использовать имя хоста, который доступен внутри Docker Compose, например, `nginx` или другой сервис, через который доступна папка `media`.

Например, если ваш Nginx раздаёт файлы, можно использовать такой URL:

ruby

Копировать код

`http://nginx/media/scraper/product/images/your_image.jpg`

### Итог:

1. Настройте общий том между контейнерами для прямого доступа к папке `media`.
2. Либо настройте HTTP-сервер (например, Nginx) для раздачи файлов из папки `media`.
3. Убедитесь, что вы используете правильный URL для доступа к изображениям между контейнерами (например, имя хоста внутри Docker Compose).