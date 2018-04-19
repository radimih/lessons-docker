# **Урок 5:**

## Файлы

**Dockerfile**

```
# ====================================================================
FROM alpine/git as clone

ARG GIT_URL
ARG GIT_TAG=master

WORKDIR /app
RUN git clone $GIT_URL --branch $GIT_TAG .

# ====================================================================
FROM maven:3-jdk-8-alpine as build

COPY --from=clone /app/pom.xml /app/
COPY --from=clone /app/src /app/src/
WORKDIR /app
RUN mvn -s /usr/share/maven/ref/settings-docker.xml package

# ====================================================================
FROM openjdk:8-jre-alpine

EXPOSE 8080

ARG ARTIFACT

COPY --from=build /app/target/${ARTIFACT}.jar /app/app.jar

ENTRYPOINT ["java","-jar","/app/app.jar"]
```

**docker-compose.yml**

```
version: "3"

services:

  service:
    image: hello-world
    container_name: hello-world
    build:
      context: .
      args:
        ARTIFACT: hello-world
        GIT_URL: https://github.com/radimih/java-hello-world.git

    ports:
      - 80:8080
    depends_on:
      - db

  db:
    image: mysql
    container_name: hello-world-mysql
    environment:
      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
    volumes:
      - mysql-db:/var/lib/mysql
    ports:
      - 3306:3306

volumes:

  mysql-db:
```

## ARG и ENV

## База данных

Запуск:

```bash
$ docker-compose up -d db
```

Инициализация БД trial:

```
$ docker-compose exec db mysql
mysql> create database trial character set utf8;
mysql> use trial
mysql> create table product(id int not null primary key, name varchar(100));
mysql> insert into product values (1, 'Product One');
mysql> insert into product values (2, 'Product Two');
mysql> exit
```

Расположение файлов БД на хост-машине:

```
$ docker volume ls
$ docker volume inspect lesson5_mysql-db
```

