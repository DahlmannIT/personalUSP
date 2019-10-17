# Full infrastructure

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




## <a name="explanation"></a> Explanation 
Docker-based backend platform using apache kafka as streaming framework, apache flink as big data framework, postgres as SQL database and apache zeppelin as developer analyze tool.

## <a name="architecture"></a> Architecture
![architecture infrastructure](/img/Architektur_17102019.svg)

## Prerequisites

## Variables  

## Ports

## <a name="dockercomposeyml"></a> docker-compose.yml

```yml
version: '3'
services:

  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    container_name: zookeeper
    hostname: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
     - 2181

  kafka:
    image: confluentinc/cp-kafka
    # build: ./kafka
    container_name: kafka
    hostname: kafka
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    ports:
     - 9092:9092
    depends_on:
     - zookeeper

  # schema-registry:
  #   image: confluentinc/cp-schema-registry
  #   container_name: schema-registry
  #   ports:
  #     - 8081:8081
  #   depends_on:
  #     - zookeeper
  #     - kafka
  #   environment:
  #     SCHEMA_REGISTRY_HOST_NAME: schema-registry
  #     SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL: zookeeper:2181

  kafka-connect:
    # image: confluentinc/cp-kafka-connect
    build: ./docker-images/kafka-connect
    container_name: kafka-connect
    hostname: kafka-connect
    environment:
      CONNECT_BOOTSTRAP_SERVERS: kafka:29092
      CONNECT_REST_ADVERTISED_HOST_NAME: connect
      CONNECT_GROUP_ID: kafka-connect-group
      CONNECT_REST_PORT: 8083
      CONNECT_CONFIG_STORAGE_TOPIC: connect-configs
      CONNECT_OFFSET_FLUSH_INTERVAL_MS: 10000
      CONNECT_OFFSET_STORAGE_TOPIC: connect-offsets
      CONNECT_STATUS_STORAGE_TOPIC: connect-status
      CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR: 1
      CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR: 1
      CONNECT_STATUS_STORAGE_REPLICATION_FACTOR: 1
      # CONNECT_KEY_CONVERTER: org.apache.kafka.connect.storage.StringConverter
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      # CONNECT_KEY_CONVERTER: io.confluent.connect.avro.AvroConverter
      # CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      CONNECT_VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      # CONNECT_VALUE_CONVERTER: io.confluent.connect.avro.AvroConverter
      # CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      CONNECT_INTERNAL_KEY_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_INTERNAL_VALUE_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      # CLASSPATH required due to CC-2422
      # CLASSPATH: /usr/share/java/monitoring-interceptors/monitoring-interceptors-5.3.1.jar
      # CONNECT_PRODUCER_INTERCEPTOR_CLASSES: "io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor"
      # CONNECT_CONSUMER_INTERCEPTOR_CLASSES: "io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor"
      CONNECT_PLUGIN_PATH: "/usr/share/java,/usr/share/confluent-hub-components"
      # CONNECT_LOG4J_LOGGERS: org.apache.zookeeper=ERROR,org.I0Itec.zkclient=ERROR,org.reflections=ERROR
    depends_on:
      - kafka
      # - schema-registry
      - postgres
    volumes:
      - ./data:/home/data
    ports:
      - 8083:8083
    # command:
    #   - /bin/bash
    #   - -c
    #   - sleep infinity & /etc/confluent/docker/run
    #   - connect-distributed etc/kafka-connect-jdbc/postgres-sink.properties

  postgres:
    image: postgres
    container_name: postgres
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: postgres
      POSTRES_PASSWORD: postgres
    volumes:
      - ./.docker-volumes/postgres-data:/var/lib/postgressql/data

  # zeppelin:
  #   image: xemulian/zeppelin
  #   container_name: zeppelin
  #   ports:
  #     - 8080:8080
  #   volumes:
  #     - /opt/zeppelin/logs
  #     - /opt/zeppelin/notebook

 # jobmanager:
 #   image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
 #   container_name: jobmanager
 #   expose:
 #     - 6123
 #   ports:
 #     - 8081:8081
 #   command: jobmanager
 #   environment:
 #     - JOB_MANAGER_RPC_ADDRESS=jobmanager
 #
 # taskmanager:
 #   image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
 #   container_name: taskmanager
 #   expose:
 #     - 6121
 #     - 6122
 #   depends_on:
 #     - jobmanager
 #   command: taskmanager
 #   links:
 #     - jobmanager:jobmanager
 #   environment:
 #     - JOB_MANAGER_RPC_ADDRESS=jobmanager
 ```

## Installation guide

- create your containers with docker-compose
  ```sh
  docker-compose up
  ```

- `./data` should not be an empty directory for now

- execute the bash-script locally (required once)
  - on Linux
    ```sh
    ./deploy-connector.sh
    ```
  - on Windows 
    ```sh
    
    ```
    


## <a name="howtouse"></a> How-to-use


## Important commands

## Interfaces

