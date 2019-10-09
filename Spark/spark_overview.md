# Spark in Docker aufsetzen
## Dockerfile
```ssh
FROM openjdk:8-alpine
RUN apk --update add wget tar bash
RUN wget http://artfiles.org/apache.org/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz
RUN tar -xzf spark-2.4.4-bin-hadoop2.7.tgz && \
    mv spark-2.4.4-bin-hadoop2.7 /spark && \
    rm spark-2.4.4-bin-hadoop2.7.tgz

```
## Powershell in Windows

### Docker Netzwerk erstellen

- Neue Powershell öffnen
- ``` docker network create spark_network ```

### Spark Container erstellen

- Powershell öffnen
- In Pfad des Dockerfile's wechseln
- ```docker build .``` oder ``` docker build -t [Name]/spark:latest . ``` mit einem Namen

### Spark Master erstellen 

- Powershell öffnen
- In Pfad des Dockerfile's wechseln
- ```docker ps --all ```
- **ID** oder **Name** des Builds aufschreiben oder Umbenennen
- ``` docker run --rm -it --name spark-master --hostname spark-master -p 7077:7077 -p 8080:8080 --network spark_network [ID] /bin/sh ```
- In der Spark-Container-Shell den Spark Master starten
- ``` /spark/bin/spark-class org.apache.spark.deploy.master.Master --ip `hostname` --port 7077 --webui-port 8080 ```
- Im Browser ``` localhost:8080 ``` öffnen

![Spark Master](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/sparkmaster.png)


### Spark Worker erstellen

- neue Powershell öffnen
- In Pfad des Dockerfile's wechseln
- ``` docker ps ``` 
- **ID** des Images notieren
- ``` docker run --rm -it --name spark-worker --hostname spark-worker --network spark_network [ID] /bin/sh ```
- In der Spark-Container-Shell den Spark Worker starten
- ``` /spark/bin/spark-class org.apache.spark.deploy.worker.Worker --webui-port 8080 spark://spark-master:7077 ```
- Der Spark-Worker registriert sich nun beim Spark-Master

- **Spark worker registriert sich beim Master (unten) und Master registriert Worker (oben)**
![Spark Worker](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/sparkworker.png)

- **WebUI**
![Spark Localhost Worker](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/localhost_worker.png)

### Spark Driver erstellen (sendet Applications ans Cluster)

- neue Powershell öffnen
- In Pfad des Dockerfile's wechseln
- ``` docker ps ```
- **ID** des Images notieren
- ``` docker run --rm -it --network spark_network [ID] /bin/sh ```
- In der Spark-Container-Shell die Application (in unserem Fall Pi berechnen) starten
- ``` /spark/bin/spark-submit --master spark://spark-master:7077 --class org.apache.spark.examples.SparkPi /spark/examples/jars/spark-examples_2.11-2.4.4.jar 1000 ```
- Die Application wird ans Cluster gesendet und bearbeitet
- Dies kann man im WebUI sehen

- **Spark arbeitet die Application ab**
![Spark Working](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/spark_working.png)

- **Spark hat die Aufgabe erledigt**
![Spark Complete](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/spark_complete.png)

- **Lösung in der Shell (Pi is roughly 3.14149499141495)**
![Spark Complete Shell](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/spark_complete2.png)


### Docker Compose erstellen (Automatisches hochfahren)

- Vorher alle Container rebuilden
	- ``` docker ps --all ```
	- ``` docker stop [Name/ID] ```
	- ``` docker rm [Name/ID] ```

- Ordner öffnen in dem das Dockerfile liegt
- Datei namens ``` start-master.sh ``` erstellen
```
#!/bin/sh
/spark/bin/spark-class org.apache.spark.deploy.master.Master \
    --ip $SPARK_LOCAL_IP \
    --port $SPARK_MASTER_PORT \
    --webui-port $SPARK_MASTER_WEBUI_PORT
```
- Datei namens ``` start-worker.sh ``` erstellen
	
```
#!/bin/sh
/spark/bin/spark-class org.apache.spark.deploy.worker.Worker \
    --webui-port $SPARK_WORKER_WEBUI_PORT \
    $SPARK_MASTER
```
- (Troubleshooting: Beide Dateien mit Notepad++ öffnen und per Edit -> EOL conversion -> ändern von CRLF zu LF. Rebuild and run)

- Dockerfile öffnen
	- ``` COPY start-master.sh /start-master.sh ``` in neuer Zeile einfügen um Script zum Starten eines Spark Masters ins Image einzubinden
	- ``` COPY start-worker.sh /start-worker.sh ``` in neuer Zeile einfügen um Script zum Starten der Spark Workers ins Image einzubinden


- Neues File namens ``` docker-compose.yml ``` erstellen
```
version: "3.3"
services:
  spark-master:
    image: [NAME]/spark:latest
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
    image: [NAME]/spark:latest
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
- beide ```[NAME]``` mit einem Namen ersetzen


- Cluster mit ``` docker-compose up --scale spark-worker=3 ``` starten
	- Worker-Anzahl ist variabel zu wählen


