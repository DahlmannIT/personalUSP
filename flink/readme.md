# Flink

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

* [Connection Flink <-> Postgres](#connection-flink<->postgres)

* [Connection Flink <-> Kafka](#connection-flink<->kafka)





## <a name="explanation"></a> Explanation 
Apache Flink is an open-source stream-processing framework developed by the Apache Software Foundation. The core of Apache Flink is a distributed streaming data-flow engine written in Java and Scala. Flink executes arbitrary dataflow programs in a data-parallel and pipelined manner. Flink's pipelined runtime system enables the execution of bulk/batch and stream processing programs. Furthermore, Flink's runtime supports the execution of iterative algorithms natively.

## <a name="architecture"></a> Architecture
![Flink architecture](http://www.heinz-schmitz.org/files/TSS-Schmitz/Nachrichtenbilder/2016/2016%2008/06-flink-stack-frontpage.jpg)

## Prerequisites

## Variables  
None

## Ports
Service | Ports 
--- | ---
Web Client | 8081:8081
JobManager RPC | 6123:6123
TaskManagers RPC | 6122:6122
TaskManagers Data | 6121:6121

## <a name="dockercomposeyml"></a> docker-compose.yml
```yml
version: "3"
services:
  jobmanager:
    image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
    expose:
      - "6123"
    ports:
      - "8081:8081"
    command: jobmanager
    environment:
      - JOB_MANAGER_RPC_ADDRESS=jobmanager

  taskmanager:
    image: ${FLINK_DOCKER_IMAGE_NAME:-flink}
    expose:
      - "6121"
      - "6122"
    depends_on:
      - jobmanager
    command: taskmanager
    links:
      - "jobmanager:jobmanager"
    environment:
      - JOB_MANAGER_RPC_ADDRESS=jobmanager
```

## Installation guide

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces

## <a name="connection-flink<->postgres"></a> Connection Flink <-> Postgres
https://github.com/DahlmannIT/personalUSP/tree/master/flink_postgres

## <a name="connection-flink<->postgres"></a> Connection Flink <-> Kafka
https://github.com/DahlmannIT/personalUSP/tree/master/kafka_flink
