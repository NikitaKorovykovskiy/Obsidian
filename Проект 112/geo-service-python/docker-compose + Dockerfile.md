```python
docker-compose.yml
version: '3.4'

services:
  server:
    build:
      context: ./
      dockerfile: ./docker/Dockerfile
    ports:
      - "8000:8000"
    environment:
      YENISEI_KEY: dcnfjreidyf5tnmnodkx7dqp
    restart: always
    volumes:
      - ./:/opt  # Монтирование текущей директории в /opt в контейнере

```

```python
Dockerfile

FROM python:3.8

COPY ../requirements.txt /tmp
RUN pip install -r /tmp/requirements.txt

ADD ../models.py /opt
ADD ../main.py /opt
ADD ../settings.py /opt
ADD ../services.py /opt

WORKDIR /opt

EXPOSE 8000
ENTRYPOINT ["uvicorn", "main:app", "--port=8000", "--host=0.0.0.0", "--reload"]
```
