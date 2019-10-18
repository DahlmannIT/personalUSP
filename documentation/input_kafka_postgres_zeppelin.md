# Input -> Kafka -> Postgres -> Zeppelin

- ```docker-compose -f docker-compose.yml up --remove-orphans``` 
- neues Terminal
- ``` cd docker-images/kafka-connect ```
- ```docker build .```
- Browser starten
- `localhost:8080`
- Interpreter wie immer
- Datenbank heißt `postgres`
- nicht vergessen, den zweiten Interpreter zu aktivieren!
- Tabelle heißt `test_topic`


- `uip-infrastructure`d irectory  
``` ./deploy-connector.sh ``` (linux) 

- daten zu "test.csv" nennen und in `data` ordner einfügen

- rest passiert automatisch -> wird in finished geworfen und in datenbank geschrieben yo

- arbeiten auf der datenbank per zeppelin sql befehle 
