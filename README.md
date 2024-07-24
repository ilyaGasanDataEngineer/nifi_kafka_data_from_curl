# NiFi Kafka Data from Curl

Этот проект настраивает поток NiFi для обработки данных, получаемых через HTTP, и маршрутизирует их в топики Kafka в зависимости от содержимого.

## Требования

- Docker
- Docker Compose

## Начало работы

1. Клонируйте репозиторий:

    ```sh
    git clone https://github.com/ilyaGasanDataEngineer/nifi_kafka_data_from_curl.git
    ```

2. Перейдите в каталог проекта:

    ```sh
    cd nifi_kafka_data_from_curl
    ```

3. Запустите Docker-контейнеры:

    ```sh
    docker-compose up
    ```

4. Импортируйте поток NiFi из файла `kafka_fromm_curls_sort_by_content.json`.

## Настройка топиков Kafka

1. Создайте топик `succses`:

    ```sh
    docker exec broker \
    kafka-topics --bootstrap-server broker:29092 \
                 --create \
                 --topic succses
    ```

2. Откройте интерактивный терминал для отправки сообщений в топик `succses`:

    ```sh
    docker exec --interactive --tty broker \
    kafka-console-producer --bootstrap-server broker:29092 \
                           --topic succses
    ```

3. Откройте другой интерактивный терминал для отправки сообщений в топик `error`:

    ```sh
    docker exec --interactive --tty broker \
    kafka-console-producer --bootstrap-server broker:29092 \
                           --topic error
    ```

## Потребление сообщений

1. Откройте первый терминал и запустите consumer для топика `error`:

    ```sh
    docker exec --interactive --tty broker \
    kafka-console-consumer --bootstrap-server broker:29092 \
                           --topic error \
                           --from-beginning
    ```

2. Откройте второй терминал и запустите consumer для топика `succses`:

    ```sh
    docker exec --interactive --tty broker \
    kafka-console-consumer --bootstrap-server broker:29092 \
                           --topic succses \
                           --from-beginning
    ```

## Запуск потока NiFi

После настройки топиков и consumer'ов вы можете запустить поток NiFi для начала обработки данных.

Запросв с консоли примеры:
curl --header "Content-Type: application/json" --request POST --data '{"message": "no error message"}' 127.0.0.1:8081/loglistener
curl --header "Content-Type: application/json" --request POST --data '{"message": "ERROR message"}' 127.0.0.1:8081/loglistener

