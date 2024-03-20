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

### Запуск скрипта для тестовой БД
Копируем скрипт внутрь контейнера
```
docker cp demo-small.sql postgres-db-1:/
```
Внутри моего докер композа
```
psql -f demo-small.sql -U habrpguser -d habrdb
```
Затем зайти в бд
```
psql -U habrpguser -d demo
```
