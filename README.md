Домашнее задание к занятию «Репликация и масштабирование. Часть 2» Брюхов А SYS-26

Задание 1
Опишите основные преимущества использования масштабирования методами:

активный master-сервер и пассивный репликационный slave-сервер;
master-сервер и несколько slave-серверов;
активный сервер со специальным механизмом репликации — distributed replicated block device (DRBD);
SAN-кластер.
Дайте ответ в свободной форме.

Решение 1:

Активный master-сервер и пассивный репликационный slave-сервер:
Преимущества:
1.Обеспечивает отказоустойчивость и высокую доступность данных.
2.Улучшает производительность путем распределения нагрузки между master и slave серверами.
3.Повышает безопасность данных и обеспечивает возможность восстановления при сбоях.

Master-сервер и несколько slave-серверов:
Преимущества:
1.Позволяет масштабироваться при увеличении количества пользователей или запросов.
2.Улучшает отказоустойчивость и обеспечивает более равномерное распределение нагрузки.
3.Повышает отказоустойчивость и обеспечивает более надежное хранение данных.

Активный сервер с DRBD:
Преимущества:
1.Обеспечивает мгновенное переключение на пассивный сервер в случае сбоя активного сервера.
2.Гарантирует целостность данных и минимизирует потери информации при сбоях.
3.Повышает доступность данных и упрощает процесс масштабирования.

SAN-кластер:
Преимущества:
1.Обеспечивает высокую отказоустойчивость за счет резервирования данных и зеркального хранения.
2.Позволяет эффективно управлять ресурсами и обеспечивать гибкое масштабирование.
3.Обеспечивает высокую производительность и надежность хранения данных.

Задание 2
Разработайте план для выполнения горизонтального и вертикального шаринга базы данных. База данных состоит из трёх таблиц:

пользователи,
книги,
магазины (столбцы произвольно).
Опишите принципы построения системы и их разграничение или разбивку между базами данных.

Пришлите блоксхему, где и что будет располагаться. Опишите, в каких режимах будут работать сервера.

Решение 2

План для горизонтального и вертикального шардинга базы данных

Структура базы данных:

Таблица "Пользователи"
  Поля: ID пользователя, Имя, Фамилия, Email, Дата регистрации, и другие связанные с пользователем данные.

Таблица "Книги"
  Поля: ID книги, Название, Автор, Год издания, Жанр, и другие связанные с книгой данные.

Таблица "Магазины"
  Поля: ID магазина, Название, Адрес, Тип магазина, и другие связанные с магазином данные.

Принципы построения системы и разграничение:

Горизонтальный шардинг:
Разделение данных по группам (шардам) на основе определенного критерия, например, по диапазону ID пользователей, книг или магазинов.
Каждый шард хранится в отдельной базе данных.

Вертикальный шардинг:
Разделение данных внутри каждой таблицы по столбцам, чтобы различные столбцы хранились в разных базах данных.
Например, одна база данных может содержать только основные данные пользователя (ID, Имя, Фамилия), а другая - более    чувствительные данные (Email, Дата регистрации).

Блоксхема:

+---------------------+
|      Приложение     |
+---------------------+
           |
+---------------------+
| База данных (Users) |
+---------------------+
     |           |
+---------+ +---------+
| Shard 1 | | Shard 2 |
+---------+ +---------+

+---------------------+
| База данных (Books) |
+---------------------+
     |           |
+---------+ +---------+
| Shard 1 | | Shard 2 |
+---------+ +---------+

+------------------------+
| База данных (Stores)    |
+------------------------+
     |           |
+---------+ +---------+
| Shard 1 | | Shard 2 |
+---------+ +---------+

Режимы работы серверов:

    Чтение:
        Чтение данных пользователя (и других сущностей) может происходить с любого из шардов.
        Реализация балансировки нагрузки для равномерного распределения запросов между шардами.

    Запись:
        Запись данных пользователя осуществляется в соответствующем шарде, выбранном на основе критериев (например, ID пользователя).

Задание 3*
Выполните настройку выбранных методов шардинга из задания 2.

Пришлите конфиг Docker и SQL скрипт с командами для базы данных.

Решение 3

Для выполнения задания мы будем использовать Docker для создания контейнеров баз данных и SQL скрипты для настройки схемы данных в каждом шарде.

1.Создадим Docker-контейнеры для каждого шарда базы данных (Users, Books, Stores).
В файле docker-compose.yml определим конфигурацию контейнеров, их сети и другие параметры.

Пример docker-compose.yml:

    version: '3'

    services:
      shard1_users_db:
        image: postgres
        environment:
          POSTGRES_DB: shard1_users
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

      shard2_users_db:
        image: postgres
        environment:
          POSTGRES_DB: shard2_users
              POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

      shard1_books_db:
        image: postgres
        environment:
          POSTGRES_DB: shard1_books
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

      shard2_books_db:
        image: postgres
        environment:
          POSTGRES_DB: shard2_books
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

      shard1_stores_db:
        image: postgres
        environment:
          POSTGRES_DB: shard1_stores
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

      shard2_stores_db:
        image: postgres
        environment:
          POSTGRES_DB: shard2_stores
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        networks:
          -shard_network

    networks:
      shard_network:
        driver: bridge

Запустим контейнеры с помощью docker-compose up -d.

2.Создадим SQL скрипты для каждого шарда, которые создадут соответствующие таблицы.

Пример SQL скрипта для Shard 1 (Users):

    -- Создание таблицы Users в Shard 1
    CREATE TABLE Users (
        ID INT PRIMARY KEY,
        FirstName VARCHAR(255),
        LastName VARCHAR(255)
        -- Дополнительные поля
    );

Пример SQL скрипта для Shard 2 (Users):

    -- Создание таблицы Users в Shard 2
    CREATE TABLE Users (
        ID INT PRIMARY KEY,
        Email VARCHAR(255),
        RegistrationDate DATE
        -- Дополнительные поля
    );

Аналогичные скрипты нужно создать для таблиц Books и Stores в каждом шарде.

3.Настроим механизмы шардинга, такие как распределенные индексы или ключи.

Пример SQL скрипта для горизонтального шардинга (используем для Users таблицы):

    -- Горизонтальный шардинг по диапазону ID
    CREATE TABLE Users (
        ID INT PRIMARY KEY,
        FirstName VARCHAR(255),
        LastName VARCHAR(255)
        -- Дополнительные поля
    ) PARTITION BY RANGE (ID);

Вертикальный шардинг:

Разделим столбцы вертикального шардинга между разными базами данных.

Пример SQL скрипта для вертикального шардинга (используем для Users таблицы):

    -- Вертикальный шардинг для Shard 1
    CREATE TABLE Shard1.Users (
        ID INT PRIMARY KEY,
        FirstName VARCHAR(255),
        LastName VARCHAR(255)
        -- Дополнительные поля
    );



    -- Вертикальный шардинг для Shard 2
    CREATE TABLE Shard2.Users (
        ID INT PRIMARY KEY,
        Email VARCHAR(255),
        RegistrationDate DATE
        -- Дополнительные поля
    );

Аналогично для таблиц Books и Stores в каждом шарде.

Права доступа:

Установим права доступа для обеспечения безопасности данных.

Пример SQL скрипта для установки прав доступа:

    -- Установка прав доступа для Shard 1 Users
    GRANT ALL PRIVILEGES ON TABLE Shard1.Users TO user;


    -- Установка прав доступа для Shard 2 Users
    GRANT ALL PRIVILEGES ON TABLE Shard2.Users TO user;

    Аналогично для таблиц Books и Stores в каждом шарде.

Эти шаги должны помочь нам настроить горизонтальный и вертикальный шардинг базы данных для трех таблиц (пользователи, книги, магазины). В приведенных примерах используются команды и синтаксис для PostgreSQL, однако, в зависимости от используемой СУБД, они могут отличаться. Необходимо адаптировать примеры в соответствии с используемой нами СУБД и ее спецификациями.


