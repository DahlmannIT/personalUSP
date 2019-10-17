# Postgres

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

## <a name="architecture"></a> Architecture
![Postgres architecture](https://upload.wikimedia.org/wikipedia/commons/5/52/PostgreSQL_processes_1.png)

## Prerequisites

## Variables  

## Ports

Service | Ports 
--- | ---
Postgres | 5432:5432

## <a name="dockercomposeyml"></a> docker-compose.yml
```yml
version: "3"
services:

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

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces
