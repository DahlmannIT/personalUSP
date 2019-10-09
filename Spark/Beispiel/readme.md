# Beispiel

- Ordner "testContainer" runterladen
- Powershell öffnen
- In Ordner "testContainer" navigieren
- ```  docker build -t spark_compose/spark:latest . ```
- ``` docker-compose up --scale spark-worker=3 ```
- Browser öffnen und ``` localhost:8080 ``` anschauen


