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

Service | Ports 
--- | ---
Spark Standalone Master (RPC) | 7077 
Spark Standalone Master (Web UI) | 8580, 8980*  
Spark Standalone Worker | 8581, 8981*  
Spark Thrift Server | 2304         
Spark History Server | 18080,18480* 
Spark External Shuffle Service (if yarn shuffle service is enabled) | 7337         
CLDB | 7222         
ZooKeeper | 5181         
Nodes running ResourceManager | 8032         
MapR Filesystem Server | 5660, 5692   

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
