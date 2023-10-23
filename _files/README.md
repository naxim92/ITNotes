# Докеризированый clickhouse

Проект описывает запуск clickhouse в докере с хранением информации о пользователях в sql, преднастроенной админской учеткой и любыми доп настройками из папки config.d или config.yaml

## Запуск clickhouse с нуля

1. Правим *.env* под себя
    - Если не нужен http порт, можно указать
    `CLICK_PUBLIC_HTTP_IP=127.0.0.1`, тогда http траффик будет доступен только внутри контейнера и с хоста по адресу localhost (не будет доступа из вне хоста по сети)
2. Запускаем скрипт
``` bash
chmod +x up-clickhous.sh
up-clickhous.sh -i
```
После чего вводим пароль для пользователя *admin* или того, кого указали в переменной *CLICKHOUSE_ADMIN_USER* в *.env*.
При запуске контейнера стартует скрипт *entrypoint-wrapper.sh*, который создает нового пользователя с правами админа *CLICKHOUSE_ADMIN_USER* и паролем, запрошенным у пользователя. Для стандартного пользователя *default* остается возможность коннектится к clickhouse из контейнера. Новый админ имеет все права, доступен для подключения отовсюду. После инициализации админской учетки запускется clickhouse через стандартный `entrypoint.sh`

## Повторный запуск clickhous

Можно также использовать скрипт `up-clickhous.sh` (без ключа *-i*).
Можно просто запускать через docker-compose:
```
docker compose up -d clickhouse
```

## При компрометации каких-либо учетных записей

1. Смотрим какие есть пользователи и права в *./data/access/*
2. Удаляем папку *./data/access/*
3. Запускаем установку clickhouse с нуля, определяем новые данные для админской учетки
4. Заново создаем все учетные записи с другими паролями и теме же привилегиями

При этом данные не должны потеряться.

## Проблемы
- Windows+WSL2+ClickHouse While inserting into clickhouse i am facing filesystem error
    https://github.com/ClickHouse/ClickHouse/issues/35036

- рпа
    https://github.com/ClickHouse/ClickHouse/issues/50945

## Пример команд
```
# запуск клиента от админа на хосте
docker run -ti --rm clickhouse/clickhouse-client -h host.docker.internal  --port 19000 -u admin --ask-password

# запуск клиента от пользователя на хосте
docker run -ti --rm clickhouse/clickhouse-client -h host.docker.internal  --port 19000 -u user --ask-password
```

```
CREATE USER user IDENTIFIED WITH sha256_password BY '123';
CREATE DATABASE test_db;
GRANT ALL ON test_db.* TO user;
```

```
SHOW DATABASES;
CREATE TABLE table_1 (id Int32) ENGINE = MergeTree() ORDER BY id;
# \/ Могут быть проблемы, см. [проблемы](## Пример команд)
INSERT INTO table_1 VALUES (1);
```