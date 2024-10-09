Но можно развернуть и через обычный образ Python, Так как если ты посмотришь в docker hub и поищешь `Python3.12-slim`,  то увидишь что он начинает свою сборку с Debian
![[Pasted image 20241009133914.png]]

А так как есть debian, то мы можем установить браузер, так как есть ОС

Dockerfile
```
# Используем Python 3.12 slim образ на основе Debian
ARG PYTHON_VERSION=3.12
FROM python:${PYTHON_VERSION}-slim AS base

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

# Установка xterm (опционально, если требуется)
RUN apt-get update && apt-get install -y xterm && rm -rf /var/lib/apt/lists/*

# Установка Google Chrome и ChromeDriver
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - && \
    echo "deb http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google.list && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends google-chrome-stable && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/* && \
    CHROME_VERSION=$(google-chrome --product-version) && \
    wget -q --continue -P /chromedriver "https://edgedl.me.gvt1.com/edgedl/chrome/chrome-for-testing/$CHROME_VERSION/linux64/chromedriver-linux64.zip" && \
    unzip /chromedriver/chromedriver* -d /usr/local/bin/ && \
    rm -rf /chromedriver

# Настройка x11vnc
RUN mkdir -p ~/.vnc && x11vnc -storepasswd 1234 ~/.vnc/passwd

# Установка pip и setuptools (Python 3.12 уже установлен в базовом образе)
RUN python3 -m pip install --upgrade pip setuptools

# Копирование requirements.txt и установка зависимостей
COPY requirements.txt .
RUN python3 -m pip install --no-cache-dir -r requirements.txt

# Копируем остальной код
COPY . .

# Установка переменных среды для Chrome и Chromedriver
ENV CHROME_DRIVER_PATH=/usr/bin/chromedriver
ENV CHROME_BIN=/usr/bin/chromium
ENV DISPLAY=:0

# Делаем скрипт entrypoint исполняемым
RUN chmod +x /app/entrypoint.sh

# Определение точки входа
ENTRYPOINT ["/app/entrypoint.sh"]
```

entrypoint.sh и docker compose такой же как в файле с ubuntu, в конце посмотри