Для разворачивания через ubuntu можно использовать такой файл

```Dockerfile
FROM ubuntu:latest

EXPOSE 5900
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
# Установка необходимых пакетов
WORKDIR /app
RUN apt-get update && apt-get install -y \
    xvfb \
    x11vnc \
    fluxbox \
    wget \
    unzip \
    gnupg \
    gcc \
    curl \
    chromium-driver \
    build-essential \
    libffi-dev \
    libssl-dev \
    zlib1g-dev \
    liblzma-dev \
    libbz2-dev \
    libreadline-dev \
    libpq-dev \
    libsqlite3-dev \
    software-properties-common \
    --no-install-recommends \
    && rm -rf /var/lib/apt/lists/*

# Install xterm
RUN apt-get update && apt-get install -y xterm

# Install Google Chrome from .deb (it's works only on x86 docker host machine, not ARM or Apple Silicon)
RUN apt-get update && \
    wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - && \
    echo "deb http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google.list && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends google-chrome-stable && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/* && \
    CHROME_VERSION=$(google-chrome --product-version) && \
    wget -q --continue -P /chromedriver "https://edgedl.me.gvt1.com/edgedl/chrome/chrome-for-testing/$CHROME_VERSION/linux64/chromedriver-linux64.zip" && \
    unzip /chromedriver/chromedriver* -d /usr/local/bin/ && \
    rm -rf /chromedriver

# Install x11vnc
RUN mkdir ~/.vnc
RUN x11vnc -storepasswd 1234 ~/.vnc/passwd

RUN wget https://www.python.org/ftp/python/3.12.7/Python-3.12.7.tgz \
    && tar -xvf Python-3.12.7.tgz \
    && cd Python-3.12.7 \
    && ./configure --enable-optimizations \
    && make altinstall

# Установка pip для Python 3.12
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py \
    && python3.12 get-pip.py

# Установка setuptools для Python 3.12
RUN python3.12 -m pip install --upgrade setuptools

COPY requirements.txt .

# Обновление списка пакетов и установка необходимых зависимостей
RUN apt-get update && apt-get install -y python3.12-venv

# Создание виртуального окружения и установка зависимостей из requirements.txt
# RUN python3.12 -m venv /py && \
#   /py/bin/pip install --upgrade pip && \
#   /py/bin/pip install --no-cache-dir -r requirements.txt && \
#   rm requirements.txt
RUN python3.12 -m pip install -r requirements.txt
# Теперь копируем остальной код, чтобы он не сбрасывал кеш на предыдущих шагах
COPY . .

ENV PYTHONPATH "${PYTHONPATH}:/app"
# Возвращаемся в папку /app
RUN chmod +x /app/entrypoint.sh

# ENV CHROME_DRIVER_PATH=/usr/bin/chromedriver
# ENV CHROME_BIN=/usr/bin/chromium
ENV DISPLAY=:0
ENV CHROME_DRIVER_PATH=/usr/bin/chromedriver
ENV CHROME_BIN=/usr/bin/chromium

ENTRYPOINT ["/app/entrypoint.sh"]
```


entrypoint.sh
```
#!/bin/bash

rm -f /tmp/.X0-lock

# Run Xvfb on dispaly 0.
Xvfb :0 -screen 0 1920x1080x16 &

# Run fluxbox windows manager on display 0.
fluxbox -display :0 &

# Run x11vnc on display 0
x11vnc -display :0 -forever -usepw &

# Add delay
sleep 10

if [ "$DJANGO_MIGRATE" = "1" ]; then
  python manage.py migrate --noinput
fi

if [ "$DJANGO_SUPERUSER_USERNAME" ] && [ "$DJANGO_SUPERUSER_PASSWORD" ] && [ "$DJANGO_SUPERUSER_EMAIL" ]; then
  python manage.py createsuperuser --noinput
fi

if [ "$COLLECT_STATIC" = "1" ]; then
  python manage.py collectstatic --noinput
fi

exec "$@"
```

### docker-compose
Обязательно указываем `shm_size`, без нее не будет работать
```
scraper:
    build:
      context: ./
      dockerfile: Dockerfile
    command: python manage.py runserver 0.0.0.0:8000
    ports:
      - "8888:8000"
      - "5901:5900"
    shm_size: '2g'
```