Запуск постгрес в докере
```
docker run --name habr-pg -e POSTGRES_PASSWORD=pgpwd4habr -d postgres
```

Подключиться к бд внутри контейнера
```
psql --username=postgres --dbname=postgres
```
или
```
psql -U postgres -d postgres
```
