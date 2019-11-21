# Insights Platform Documentation

## 1. Getting started

### 1.1 Introduction

This software builds a powerful insights-platform to persist, analyze and work with big-data streams.

The infrastructure consists of the distributed streaming-platform `Apache Kafka` to build a real time data pipeline for multiple data sources to use. Take note that, as a message streaming service, Apache Kafka needs to run `Apache ZooKeeper` for robust synchronization of naming and configuration data as well as keeping track of the Kafka nodes, topics, partitions etc. The data will automatically be cleaned and persisted in a `PostgreSQL` ORDBMS. To stream-process the data, connect `Apache Flink`to the right Kafka-Consumer and deploy your job. Analyzed data and results can also be persisted in PostgreSQL. For a more visual view of your database, please refer to `Apache Zeppelin`. 



### 1.2 Use Cases

### 1.3 Prerequisites

* [JDK8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) has to be installed
* [Docker](https://www.docker.com) has to be installed
* [Docker-Compose](https://docs.docker.com/compose/install/) has to be installed

### 1.4 Quick start

Perform the following two steps (required only once)

 * move a file called `transaction_data.csv` to the `data` directory to be able to deploy a connector (next step)
 * execute the `deploy-connector.sh` file with 
    * `bash deploy-connector.sh` OR `./deploy-connector.sh`

In your terminal, navigate to `docker-compose.yml` file and start a cluster

 * `sudo docker-compose up`

For destroying a cluster, type

​	* `sudo docker-compose down` 

### 1.5 Installation Guide 



## 2. Frameworks

## 3. How to use

### 3.1  Reading data

To let Kafka read your data, just move it to the `data` folder. 
Kafka will automatically process your data according to your schemes.

If the data is parsed correctly, it will be moved to the `data/finish` folder, else you can find your data in `data/error` directory. 

To inspect and adjust the schemes, take a look at the `deploy-connector.sh` file.

For adding a new data source, take the following steps:

 * create a new connector (.csv) scheme
 * create a new `InputFilePattern`
 * create a new topic inside the connector
    * if your topic needs to be persisted into the database, add `_persist` at the end of the name

### 3.2 Make data persistable

* to persist data, we need a `PRIMARY KEY` for your data, therefore we let Flink do its wonders

* lets say, your original data source writes to `Input`-topic in Kafka

* deploy `KeyHashingJob.jar` into Flink, whereas the InputTopic = `Input` and OutputTopic = `Input_persist`

* Flink will generate Primary Keys for your data, so it can be persisted

### 3.3  Deploying Flink-job

* `localhost:8081`

* Submit new Job -> upload & start jar

//* Type in field "Program Arguments" "--input-topic transaction_data" and submit

  * Flink-examples in Jonathan Github

* Entwickler entscheidet in JAR ob aus Postgres gelesen wird und wo es gespeichert wird (Postgres, ElasticSearch)

### 3.4 Accessing PostgreSQL

Access the postgres-container bash

  *  `docker exec -it postgres bash

Connect to `psql`with username: postgres - password: postgres  

  * `psql postgres postgres`

Connect to your preferred database with `\c <database>`  

  * `\c`

Print all tables   

  * `\dt`

enter any `SQL`-commands, ending with a `;`

  * `select * from test_topic;`

### 3.5 Explore data with Zeppelin

* Fast Data Exploration on database via multiple interpreters

* Access GUI @ `localhost:8080`

* create new Notebook

* choose Interpreter and start your coding with e.g. 

  ```sh
  %sql 
  select * from test_topic;
  ```

### 3.6 Monitoring container status with Grafana

* monitoring container-status, based on Prometheus

* `localhost:3000`

  ``` 
  user: admin
  password: password
  ```

* Home -> Kafka-Overview

### 3.7 Raw monitoring with Prometheus

Access Prometheus by visiting 

​	* `localhost:9090` 



## 4. Developers

### 4.1 docker-compose.yml

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
    build: ./docker-images/kafka
    container_name: kafka
    hostname: kafka
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_OPTS: -javaagent:/usr/app/jmx_prometheus_javaagent.jar=7071:/usr/app/prom-jmx-agent-config.yml
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
     - 9092:9092
    depends_on:
     - zookeeper

  kafka-connect:
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
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_INTERNAL_KEY_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_INTERNAL_VALUE_CONVERTER: "org.apache.kafka.connect.json.JsonConverter"
      CONNECT_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      CONNECT_PLUGIN_PATH: "/usr/share/java,/usr/share/confluent-hub-components"
    depends_on:
      - kafka
      - postgres
      - zookeeper
    volumes:
      - ./data:/home/data
    ports:
      - 8083:8083

  postgres:
    image: postgres
    container_name: postgres
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: postgres
      POSTRES_PASSWORD: postgres
    volumes:
      - ./.docker-volumes/postgres-data:/var/lib/postgresql/data
      - ./.docker-volumes/postgres-init:/docker-entrypoint-initdb.d/

  zeppelin:
    image: xemuliam/zeppelin
    container_name: zeppelin
    ports:
      - 8080:8080
    volumes:
      - /opt/zeppelin/logs
      - /opt/zeppelin/notebook
      - ./.docker-volumes/zeppelin-conf:/opt/zeppelin/conf

  jobmanager:
    image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
    container_name: jobmanager
    expose:
     - 6123
    ports:
     - 8081:8081
    command: jobmanager
    environment:
     - JOB_MANAGER_RPC_ADDRESS=jobmanager

  taskmanager:
    image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
    container_name: taskmanager
    expose:
     - 6121
     - 6122
    depends_on:
     - jobmanager
    command: taskmanager
    links:
     - jobmanager:jobmanager
    environment:
     - JOB_MANAGER_RPC_ADDRESS=jobmanager

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    user: root
    ports:
      - 9090:9090/tcp
    volumes:
      - ./.docker-volumes/prometheus:/etc/prometheus
    depends_on:
      - kafka

  grafana:
    image: grafana/grafana:6.1.1
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=password
    volumes:
      - ./.docker-volumes/grafana_data:/var/lib/grafana
    depends_on:
      - "prometheus"
```

### 4.2 Environment Variables

#### 4.2.1 ZooKeeper

The ZooKeeper image uses variables prefixed with `ZOOKEEPER_` with the variables expressed exactly as they would appear in the `zookeeper.properties` file. As an example: for `clientPort` and `tickTime`, you can use 

```yml
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
```

Please refer to [Confluent Documentation](https://docs.confluent.io/current/installation/docker/config-reference.html) for a more details. 

#### 4.2.2 Kafka

The Kafka image uses variables prefixed with `KAFKA_`. 

#### 4.2.3 Kafka-Connect

#### 4.2.4 PostgreSQL

#### 4.2.5 Zeppelin

#### 4.2.6 Jobmanager

#### 4.2.7 Taskmanager

#### 4.2.8 Prometheus

#### 4.2.9 Grafana

### 4.3  Connectors

## 5. Examples
