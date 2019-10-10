# Beispiel

## Daten kopieren
- Ordner "testContainer" runterladen

## Image erzeugen
- Powershell öffnen
- In Ordner "testContainer" navigieren
- ```  docker build -t spark_compose/spark:latest . ```

## Cluster hochfahren (1 Master, 3 Worker)

- ``` docker-compose up --scale spark-worker=3 ```
![Cluster hochgefahren](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_cluster_started.png)

- Browser öffnen und ``` localhost:8080 ``` anschauen
![Cluster hochgefahren](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_hochgefahren.png)


## Driver starten und App auf Cluster feuern
- neue Powershell öffnen
- ``` docker run --rm -it --network testcontainer_spark-network spark_compose/spark /bin/sh ``` wobei "testcontainer_spark-network" der Netzwerkname und "spark_compose/spark" der Image-Name ist 
![Driver starten](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_driver_starten.png)

- in der Spark-Shell (#Einfügen mit Rechtsklick) ``` /spark/bin/spark-submit --master spark://spark-master:7077 --class org.apache.spark.examples.SparkPi /spark/examples/jars/spark-examples_2.11-2.4.4.jar 1000 ```
![App auf Cluster feuern](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_starting_app.png)

- Im WebUI sieht es nun so aus
![WebUI Running Apps](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_running_app.png)

## Endergebnis

- Das Endergebnis
![Cluster completed App](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_complete.png)

- WebUI completed App
![WebUI completed App](https://github.com/DahlmannIT/personalUSP/blob/master/Spark/img/compose_completed_app.png)



---
Referenz: [A Journey Into Big Data with Apache Spark: Part 1](https://towardsdatascience.com/a-journey-into-big-data-with-apache-spark-part-1-5dfcc2bccdd2) von Ashley Broadley
