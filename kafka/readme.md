# Kafka

* [Explanation](#explanation)

* [Architecture](#architecture)

* [Prerequisites](#prerequisites)

* [Variables](#variables)

* [Ports](#ports)

* [docker-compose.yml](#dockercomposeyml)

* [Installation guide](#installation-guide)

* [How-to-use](#howtouse)

* [Important commands](#important-commands)

* [Interfaces](#interfaces)

* [Connection Kafka <- Input](#connection-kafka<-input)

* [Connection Kafka <-> Flink](#connection-kafka<->flink)

* [Connection Kafka <-> Flink](#connection-kafka<->postgres)




## <a name="explanation"></a> Explanation 
Apache Kafka is an open-source stream-processing software platform developed by LinkedIn and donated to the Apache Software Foundation, written in Scala and Java. The project aims to provide a unified, high-throughput, low-latency platform for handling real-time data feeds. Kafka can connect to external systems (for data import/export) via Kafka Connect and provides Kafka Streams, a Java stream processing library.

## <a name="architecture"></a> Architecture
![Kafka architecture](https://www.tutorialspoint.com/apache_kafka/images/cluster_architecture.jpg)

## Prerequisites

## Variables  

## Ports

## <a name="dockercomposeyml"></a> docker-compose.yml
```
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    ports:
      - "2181"
    hostname: zookeeper
  kafka:
    image: confluentinc/cp-kafka:latest
    ports:
      - 9092:9092
    hostname: kafka
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    depends_on:
      - "zookeeper"
```

## Installation guide

navigate to the folder in which the .yml file is and get docker-compose up
```
docker-compose -f docker-compose.yml up
```
Creating of an topic. Make sure kafka is running and up. to create a kafka topic use..
```
docker-compose exec kafka kafka-topics --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic your_topic_name
```
Create and connect a producer to the topic in order to write in the topic
```
docker-compose exec kafka kafka-console-producer --broker-list localhost:9092 --topic your_topic_name
```
Create and connect a consumer to the topic in order to read from the topic
```
docker-compose exec kafka kafka-console-consumer --bootstrap-server localhost:9092 --topic your_topic_name --from-beginning
```


## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces

## <a name="connection-kafka<-input"></a> Connection Kafka <-> Input
https://github.com/DahlmannIT/personalUSP/tree/master/input_kafka

## <a name="connection-kafka<->flink"></a> Connection Kafka <-> Flink
https://github.com/DahlmannIT/personalUSP/tree/master/kafka_flink

## <a name="connection-kafka<->postgres"></a> Connection Kafka <-> Postgres
https://github.com/DahlmannIT/personalUSP/tree/master/kafka_postgres
