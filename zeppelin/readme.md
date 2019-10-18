# Zeppelin

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

* [Connection Zeppelin <- Postgres](#connection-zeppelin<-postgres)




## <a name="explanation"></a> Explanation
Apache Zeppelin is a web-based notebook that enables interactive data analytics

## <a name="architecture"></a> Architecture
![Zeppelin architecture](https://miro.medium.com/max/638/0*nDidLEaoerUYvpKA.)

## Prerequisites

## Variables  

## Ports

Service | Ports 
--- | ---
Zeppelin | 8080:8080

## <a name="dockercomposeyml"></a> docker-compose.yml

```yml
version: "3"
services:

  zeppelin:
      image: dylanmei/zeppelin
      container_name: zeppelins
      ports:
          - 8080:8080
```

## Installation guide
- navigate to docker-compose.yml folder
- build and start container:
  ```shell
  sudo docker-compose up
  ```   

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces

## <a name="connection-zeppelin<-postgres"></a> Connection Zeppelin <- Postgres
https://github.com/DahlmannIT/personalUSP/tree/master/postgres_zeppelin

