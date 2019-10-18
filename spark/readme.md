# Spark-Guide

[Spark Overview](https://github.com/DahlmannIT/personalUSP/blob/master/spark/spark_overview.md)

[Spark Step by Step](https://github.com/DahlmannIT/personalUSP/blob/master/spark/spark_stepbystep.md)

[Spark Beispiel (Pi im Cluster berechnen)](https://github.com/DahlmannIT/personalUSP/tree/master/spark/Beispiel)

# Apache Spark 

* [Explanation](#frameworks-explanation)

* [Architecture](#frameworks-architecture)

* [Prerequisites](#prerequisites)

* [Variables](#variables)

* [Ports](#ports)

* [docker-compose.yml](#dockercomposeyml)

* [Installation guide](#installation-guide)

* [How-to-use](#howtouse)

* [Important commands](#important-commands)

* [Interfaces](#interfaces)




## <a name="frameworks-explanation"></a> Framework's explanation 

## <a name="frameworks-architecture"></a> Framework's architecture

![Spark Architecture](https://jaceklaskowski.gitbooks.io/mastering-apache-spark/images/sparkapp-sparkcontext-master-slaves.png)

## Prerequisites

Prerequisite | Version 
------------ | -------
jdk | 8

## Variables  

Path | Variable | Value | Explanation 
---- | -------- | -------- | -----------
docker-compose.yml | SPARK_LOCAL_IP | spark-master | 
docker-compose.yml | SPARK_MASTER_PORT | 7077 | 
docker-compose.yml | SPARK_MASTER_WEBUI_PORT | 8080 |
docker-compose.yml | SPARK_MASTER | spark://spark-master:7077 |
docker-compose.yml | SPARK_WORKER_WEBUI_PORT | 8080 |


## Ports

Service | Ports 
--- | ---
Spark Standalone Master (RPC) | 7077 
Spark Standalone Master (Web UI) | 8080 
Spark Standalone Worker | 8080        
         


## <a name="dockercomposeyml"></a> docker-compose.yml

```yml
version: "3.3"
services:
  spark-master:
    image: spark_compose/spark:latest
    container_name: spark-master
    hostname: spark-master
    ports:
      - "8080:8080"
      - "7077:7077"
    networks:
      - spark-network
    environment:
      - "SPARK_LOCAL_IP=spark-master"
      - "SPARK_MASTER_PORT=7077"
      - "SPARK_MASTER_WEBUI_PORT=8080"
    command: "/start-master.sh"
  spark-worker:
    image: spark_compose/spark:latest
    depends_on:
      - spark-master
    ports:
      - 8080
    networks:
      - spark-network
    environment:
      - "SPARK_MASTER=spark://spark-master:7077"
      - "SPARK_WORKER_WEBUI_PORT=8080"
    command: "/start-worker.sh"
networks:
  spark-network:
    driver: bridge
    ipam:
      driver: default
```

## Installation guide

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces

- Spark **Core**: Basisfunktionen und Infrastruktur (Ein-, Ausgabe, Verteilung u.ä.)
- Spark **Streaming**: Microbatching-Fähigkeiten zur Stream-Verarbeitung
- Spark **SQL**: SQL-Zugriff auf die Daten
- Spark **ML**: Machine Learning
- Spark **GraphX**: Graph-Verarbeitung





