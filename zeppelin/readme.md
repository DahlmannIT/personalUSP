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




## <a name="explanation"></a> Explanation 

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
      image: xemuliam/zeppelin
      container_name: zeppelin
      ports:
          - 8080:8080
      volumes:
          - /opt/zeppelin/logs
          - /opt/zeppelin/notebook

```

## Installation guide

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces
