# Dokumentation (Jay, Christian)


* [Framework's explanation](#frameworks-explanation)

* [Framework's architecture](#frameworks-architecture)

* [Prerequisites](#prerequisites)

* [Environment Variables](#variables)

* [Ports](#ports)

* [docker-compose.yml](#dockercomposeyml)

* [Installation guide](#installation-guide)

* [How-to-use](#howtouse)

* [Important commands](#important-commands)

* [Interfaces](#interfaces)




## <a name="frameworks-explanation"></a> Framework's explanation 

## <a name="frameworks-architecture"></a> Framework's architecture

![Architektur 17.10.2019](/img/Architektur_17102019.svg)

  The infrastructure consists of the distributed streaming-platform `Apache Kafka` to build a real time data pipeline
  for multiple data sources to use. Take note that, as a message streaming service, Apache Kafka needs to run `Apache ZooKeeper` for robust synchronization of naming and configuration data as well as keeping track of the Kafka nodes, topics, partitions etc.
  The data will automatically be cleaned and persisted in a `PostgreSQL` ORDBMS. To stream-process the data, connect `Apache Flink`to the right Kafka-Consumer and deploy your job. Analyzed data and results can also be persisted in PostgreSQL.
  For a more visual view of your database, please refer to `Apache Zeppelin`.
  

## Prerequisites

* Docker installed

* Docker-Compose installed

* (JDK8 installed)

## Environment Variables  

### ZooKeeper

The ZooKeeper image uses variables prefixed with `ZOOKEEPER_` with the variables expressed exactly as they would appear in the `zookeeper.properties` file. As an example: for `clientPort` and `tickTime`, you can use 

```yml
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
``` 

Please refer to [Confluent Documentation](https://docs.confluent.io/current/installation/docker/config-reference.html) for a more details. 

### Kafka

The Kafka image uses variables prefixed with `KAFKA_`. 

### Kafka-Connect

### PostgreSQL

### Zeppelin

### Jobmanager

### Taskmanager

### Prometheus

### Grafana

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

## Installation guide

Start a cluster

  * `sudo docker-compose up`
  
Destroy a cluster

  * `sudo docker-compose down`

* required only once:

  * it is necessary to have a `transaction_data.csv`-File in your `data` directory to deploy the connector (next step)

  * `bash deploy-connector.sh` or `./deploy-connector.sh` (for UNIX systems only)

## <a name="howtouse"></a> How-to-use


### Reading data

* To let Kafka read your data, just put it in the `data` folder

* Kafka will automatically process your data according to your schemes

* correctly parsed data will be moved to the `data/finish` directory

* if parsing failed, you will find your data in `data/error`

* to see and adjust the schemes, take a loot at the `deploy-connector.sh` file

* to add a new data source:
  
   * create a new connector (.csv) scheme
   
   * create a new `InputFilePattern` 
   
   * create a new topic (inside the connector)
   
    * if you want your topic to be persisted into the database, add `_persist` at the end of the name
    
    
### Make data persistable

* to persist data, we need a `PRIMARY KEY` for your data, therefore we let Flink do its wonders

* lets say, your original data source writes to `Input`-topic in Kafka

* deploy `KeyHashingJob.jar` into Flink, whereas the InputTopic = `Input` and OutputTopic = `Input_persist`

* Flink will generate Primary Keys for your data, so it can be persisted


### Flink

* `localhost:8081`

* Submit new Job -> upload & start jar

//* Type in field "Program Arguments" "--input-topic transaction_data" and submit
  * Flink-examples in Jonathan Github

* Entwickler entscheidet in JAR ob aus Postgres gelesen wird und wo es gespeichert wird (Postgres, ElasticSearch)


### Postgres

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

### Zeppelin

* Fast Data Exploration on database via multiple interpreters

* Access GUI @ `localhost:8080`

* create new Notebook

* choose Interpreter and start your coding with e.g. 
  ```sh
  %sql 
  select * from test_topic;
  ```

### Grafana

* monitoring container-status, based on Prometheus

* `localhost:3000`

  ``` 
  user: admin
  password: password
  ```

* Home -> Kafka-Overview


### Prometheus

* plain container monitoring

* `localhost:9090`

## Important commands

## Interfaces

## Trouble Shooting
