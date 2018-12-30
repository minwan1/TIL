## docker compose

## compose 실행 명령어
```
docker-compose up -d
```

```
version: '3'

services:
  rabbitmq:
    container_name: soda.rabbitmq
    image: rabbitmq:3.7-management
    ports:
    - "5672:5672"
    - "15672:15672"
    environment:
        - RABBITMQ_DEFAULT_USER = user
        - RABBITMQ_DEFAULT_PASS = user

    hostname: test
# Rabbitmq uses the hostname as part of the folder name in the mnesia directory


    volumes:
          - ./volumes/rabbitmq:/var/lib/rabbitmq
#          - ./volumes/rabbitmqconfig/config:/etc/rabbitmq/

  mysql:
     container_name: soda.mysql
     image: mysql/mysql-server:5.7
     environment:
      MYSQL_ROOT_HOST: '%'
      MYSQL_DATABASE: "sodatransfer"
      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
     ports:
     - "3306:3306"
     volumes:
     - ./volumes/mysql:/var/lib/mysql
     
     
```



version: '3'

services:
  rabbitmq:
    container_name: soda.rabbitmq
    image: rabbitmq:3.7-management
    ports:
    - "5672:5672"
    - "15672:15672"
    environment:
        - RABBITMQ_DEFAULT_USER = user
        - RABBITMQ_DEFAULT_PASS = user
    hostname: soda
    volumes:
          - ./volumes/rabbitmq:/var/lib/rabbitmq

  mysql:
     container_name: soda.mysql
     image: mysql/mysql-server:5.7
     environment:
      MYSQL_ROOT_HOST: '%'
      MYSQL_DATABASE: "sodatransfer"
      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
     ports:
     - "3306:3306"
     volumes:
     - ./volumes/mysql:/var/lib/mysql


