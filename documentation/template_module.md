# Title (e.g. Module-Documentation Kafka<->Flink)

* [Framework's explanation](#frameworks-explanation)

* [Framework's architecture](#frameworks-architecture)

* [Prerequisites](#prerequisites)

* [Variables](#variables)

* [Ports](#ports)

* [docker-compose.yml](#dockercomposeyml)

* [Installation guide](#installation-guide)

* [How-to-use](#howtouse)

* [Important commands](#important-commands)

* [Interfaces](#interfaces)




## <a name="frameworks-explanation"></a> Framework's explanation 

> Kurze Beschreibung der Frameworks und der Aufgaben bzw. der Interaktionen zwischeneinander.

## <a name="frameworks-architecture"></a> Framework's architecture

> Bild der Architektur der Frameworks und kurze Erläuterung.

## Prerequisites

> Tabelle mit möglichen Prerequisites

Prerequisite | Version 
------------ | -------
bla | 0.8.15
docker-compose | latest

## Variables  

Path | Variable | Value | Explanation 
---- | -------- | -------- | -----------
docker-compose.yml | POSTGRES_USER | postgres | Username for Postgres access
docker-compose.yml | POSTGRES_PASSWORD | queenthe3 | Password for Postgres access

## Ports

Service | Ports 
--- | ---
Zeppelin | 8080:8080
Postgres | 5432:5432

## <a name="dockercomposeyml"></a> docker-compose.yml

```yml
version: "3"
services:

  zeppelin:
      image: xemuliam/zeppelin
      container_name: zeppelin
      ports:
          - 8080:8080
      volumes:
          - /opt/zeppelin/logs
          - /opt/zeppelin/notebook

  postgres:
      image: postgres:latest
      restart: always
      container_name: postgres
      environment:
          - POSTGRES_USER=postgres
          - POSTGRES_PASSWORD=queenthe3
      ports:
          - "5432:5432"
      volumes:
          - ./postgres-data:/var/lib/postgresql
```

## Installation guide

> Wie installiert/konfiguriert man den Container?

## <a name="howtouse"></a> How-to-use

> Step by step guide 

## Important commands

> Nützliche/Oft verwendete Commands mit kurzer Beschreibung

- Docker-Compose-Images hochfahren mit `docker-compose` :+1:, e.g.
    ```shell
    sudo docker-compose up -d
    ```

## Interfaces

> Anbindungen nach außen
