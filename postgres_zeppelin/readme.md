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

## Ports

## <a name="dockercomposeyml"></a> docker-compose.yml

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
  - print the path using `pwd` and copy it, e.g.
    ```shell
    /opt/zeppelin/interpreter/jdbc
    ```
  - print the name of your **jdbc.jar** file using `ls` and copy it, e.g.
    ```shell
    zeppelin-jdbc-0.8.0.jar
    ``` 
    

## <a name="howtouse"></a> How-to-use

## Important commands

## Interfaces


