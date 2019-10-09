# Beispiel

- Ordner "testContainer" runterladen
- Powershell öffnen
- In Ordner "testContainer" navigieren
- ```  docker build -t spark_compose/spark:latest . ```
- ``` docker-compose up --scale spark-worker=3 ```
- Browser öffnen und ``` localhost:8080 ``` anschauen

- neue Powershell öffnen
- ``` docker run --rm -it --network testcontainer_spark-network spark_compose/spark /bin/sh ``` wobei "testcontainer_spark-network" der Netzwerkname und "spark_compose/spark" der Image-Name ist 
- in der Spark-Shell ``` /spark/bin/spark-submit --master spark://spark-master:7077 --class org.apache.spark.examples.SparkPi /spark/examples/jars/spark-examples_2.11-2.4.4.jar 1000 ```


