## docker compose

## compose 실행 명령어
```
docker-compose up -d
```

## rabbitmq
```
version: '3'
services:
  rabbitmq:
    container_name: service.rabbitmq
    image: rabbitmq:3.7
    ports:
    - "5672:5672"

```