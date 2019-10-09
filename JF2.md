# Infrastruktur-Team
## TODO bis 09.10. - 15:00 Uhr
### Docker einarbeiten


### Framework Kafka
https://ipt.ch/apache-kafka-und-was-es-mit-dem-hype-auf-sich-hat/
**Vorteile**  
```ssh

```
**Nachteile**  
```ssh

```
**Schnittstellen**

- Alles in Java, PHP, Python, C/C++, Ruby, Perl oder Go
-   **Kafka Producer**: Das Producer-API ermöglicht Anwendungen, Datenströme an den bzw. die Broker eines Apache-Cluster zu senden, um diese zu kategorisieren und zu speichern (in den bereits erwähnten Topics).
-   **Kafka Consumer**: Über die Consumer-API erhalten Apache-Kafka-Consumer Lesezugriff auf Daten, die in den Topics des Clusters gespeichert sind.
-   **Kafka Streams**: Das Streams-API erlaubt es einer Anwendung, als Stream-Prozessor zu fungieren, um eingehende Datenströme in ausgehende Datenströme umzuwandeln.
-   **Kafka Connect**: Dank des Connect-API ist es möglich, wiederverwendbare Producer und Consumer einzurichten, die Kafka-Topics mit existierenden Applikationen oder Datenbanksystemen verbinden.
-   **Kafka AdminClient**: Die Schnittstelle „AdminClient“ dient der einfachen Administration und Inspektion des Kafka-Clusters.

**Ports**
```ssh
Default: 9092 (server.properties)
Im Cluster: 2888, 3888
	- ClientConnection: 2181
	- FollowerConnection: 2888 (if leader)
	- else & other connections: 3888

```
**Datenanbindung**
```ssh

```
**Wie testet man?**
```ssh

```

### Framework Spark
**Vorteile**  
```ssh
- In-Memory Verarbeitung -> Schnelle Datenverarbeitung
- Nahe-Echtzeitverarbeitung
- Maschinelles Lernen
- Datensätze verknüpfbar
- Graph-Verarbeitung möglich
- Schnelle iterative Verarbeitung, da RDDs verteilt in-memory verarbeitet werden können
```
**Nachteile**  
```ssh
- Weniger Daten im Vgl. zu Hadoop verarbeitbar wegen In-Memory
- Teuerer im Gegensatz zu z.B. Hadoop
```

**Schnittstellen**  
```ssh
Spark-Shell per Scala (Zugriff auf Java-Bibliotheken) oder Python.

```
**Ports**  
| Service                                                             | Ports | 
| ------------------------------------------------------------------- | ----- |
| Spark Standalone Master (RPC)    | 7077        |
| Spark Standalone Master (Web UI) | 8580, 8980* |
| Spark Standalone Worker | 8581, 8981* |
| Spark Thrift Server | 2304 |
| Spark History Server | 18080,18480* |
| Spark External Shuffle Service (if yarn shuffle service is enabled) | 7337  |
| CLDB | 7222 |
| ZooKeeper | 5181 |
| Nodes running ResourceManager | 8032 |
| MapR Filesystem Server | 5660, 5692 |

**Datenanbindung**  
```ssh
Batch: 
	Benötigt: SparkContext, RDD (Resilient Distributed Dataset), Dataset

RDD = verteilte, fehlertolerante Collections in einem Spark Cluster (entweder aus HDFS lesen oder lokal anlegen und verteilen)
```
```java

SparkConf conf = new SparkConf()
			.setAppName("Foobar")
			.setMaster("[*]local");
JavaSparkContext sc = new JavaSparkContext(conf);

JavaRDD<String> text = sc.textFile("/tmp/datei.txt");
long ergebnis = text.filter(line -> line.count...);
sc.close();
```
**Wie testet man?**
```ssh
- per Spark Shell: 
	you can examine physical and logical query plans, partitioning strategy and preservation, and the state of your data with many different functions like `toDebugString`, `explain`, `glom`, `show`, `printSchema`, and so on

- integration testing:
	https://stackoverflow.com/questions/43729262/how-to-write-unit-tests-in-spark-2-0
	
```
**Spark Bibliotheken**
- Spark **Core**: Basisfunktionen und Infrastruktur (Ein-, Ausgabe, Verteilung u.ä.)
- Spark **Streaming**: Microbatching-Fähigkeiten zur Stream-Verarbeitung
- Spark **SQL**: SQL-Zugriff auf die Daten
- Spark **ML**: Machine Learning
- Spark **GraphX**: Graph-Verarbeitung


--------
--------
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
- Powershell öffnen
- In Pfad des Dockerfile's wechseln
- ```docker build .```
- **ID** des Builds aufschreiben oder Umbenennen
- ```docker run --rm -it [ID] /bin/sh```
- In der Container-Shell den Spark Master starten
- ```/spark/bin/spark-class org.apache.spark.deploy.master.Master --ip `hostname` --port 7077 --webui-port 8080```



