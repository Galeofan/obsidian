1) Поставить нужный образ бд
2) Выполнить команду из git bash терминала:
```bash 
docker run --name my-postgres \
  -e POSTGRES_HOST_AUTH_METHOD=trust \
  -v $(pwd)/init.sql:/docker-entrypoint-initdb.d/init.sql \
  -p 5432:5432 \
  -d postgres:15.8-alpine
```