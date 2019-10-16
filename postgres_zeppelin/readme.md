# Anbindung Postgres -> Zeppelin

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

## <a name="frameworks-architecture"></a> Framework's architecture

## Prerequisites

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
- download and unzip `postgres_zeppelin.zip` from repository
- open `Terminal`
- navigate to unzipped data folder
- build and start containers:
  ```shell
  sudo docker-compose up
  ```   
  - if there are other containers stopping the boot-up, try:
    ```shell
    sudo docker-compose up --remove-orphans
    ```
- find the jdbc-driver in zeppelin-bash
  ```shell
  sudo docker exec -it zeppelin bash
  ```
  - use `ls` and `cd` to navigate into **interpreter** -> **jdbc**
  - print the path using `pwd` and copy it, in this case:
    ```shell
    /opt/zeppelin/interpreter/jdbc
    ```
  - print the name of your **jdbc.jar** file using `ls` and copy it, in this case:
    ```shell
    postgresql-9.4-1201-jdbc41.jar
    ``` 
- to create a database with POSTGRES, access the postgres shell with (note that the second `postgres` is the username):
  ```shell
  sudo docker exec -it postgres psql -U postgres
  ```
  - create the database `my_db`
    ```shell
    CREATE DATABASE my_db;
    ``` 
- to create a database with ZEPPELIN, access the zeppelin webUI via `localhost:8080` in your browser
  - click on `anonymous` -> `interpreter`, choose `jdbc` and click `edit`
  - change the following settings:
    - default.url: `jdbc:postgresql://postgres:5432/`
    - default.password: `queenthe3`
    - default.user: `postgres`
    - artifact: `/opt/zeppelin/interpreter/jdbc/postgresql-9.4-1201-jdbc41.jar`
      (jdbc-path + jar)
  - click `save`
  - click `notebook` -> `create new` and pick a random name like `my_notebook`
  - click the cogwheel and set the default interpreter to `jdbc` by drag'n'dropping it at the first place
  - click `save`
  - to create a database, change to your newly created notebook and insert
  ```sql
  %jdbc
  create database my_db;
  ```
    

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces


