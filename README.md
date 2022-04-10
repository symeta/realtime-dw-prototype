# Set up a Debezium Server

## 1. Create an EC2 instance as the host of the Debezium Server.
The instance shoud be provisioned in the same VPC with the other resources. Make sure that java is installed 

```sh
sudo yum install java
java --verison
```

## 2.  Get access to the EC2 instance and download the Debezium Server package.

```sh
wget https://repo1.maven.org/maven2/io/debezium/debezium-server-dist/1.8.1.Final/debezium-server-dist-1.8.1.Final.tar.gz
```
and untar the package, go to the debezium-server/conf dir

```sh
tar -zxvf debezium-server-dist-1.8.1.Final.tar.gz
cd debezium-server/conf
```

## 3. Edit the application.properties file as below. (generate the file from the give template named as application.properties.example)

```vim
debezium.sink.type=kafka
debezium.sink.kafka.producer.bootstrap.servers=<MSK Bootstrap Server Private endpoint> #3 servers with 9092 as the port
debezium.sink.kafka.producer.key.serializer=org.apache.kafka.common.serialization.StringSerializer
debezium.sink.kafka.producer.value.serializer=org.apache.kafka.common.serialization.StringSerializer
debezium.source.connector.class=io.debezium.connector.mysql.MySqlConnector
debezium.source.offset.storage.file.filename=data/offsets.dat # needs to be created in the debezium-server dir
debezium.source.offset.flush.interval.ms=0
debezium.source.database.hostname=<Mysql Instance endpoint>
debezium.source.database.port=3306
debezium.source.database.user=<Mysql Instance username>
debezium.source.database.password=<Mysql Instance password>
debezium.source.database.dbname=salesdb #can be any name, salesdb is used because we create that database previously
debezium.source.database.server.name=tutorial #can be any name, this term is the prefix of the kafka topic
debezium.source.database.history=io.debezium.relational.history.KafkaDatabaseHistory
debezium.source.database.history.kafka.bootstrap.servers=<MSK Bootstrap Server Private endpoint>
debezium.source.database.history.kafka.topic=mysql-cdc-history #can be any name, no need to change if you do not have specific intentions
debezium.source.database.include.list=salesdb #the same with dbname
debezium.format.key=json
debezium.format.value=json
quarkus.log.console.json=false #to make sure that the console log is readable
```
## 4. Install Kafka Client on the EC2 instance

```sh
mkdir kafka && cd kafka
wget https://archive.apache.org/dist/kafka/2.3.1/kafka_2.12-2.3.1.tgz
tar -xzf kafka_2.12-2.3.1.tgz --strip 1
cd bin
```
## 5. Check that the topics are created in MSK

```sh 
./kafka-topics.sh --bootstrap-server <MSK Bootstrap Server Private endpoint> --list
```
The output should be like the below snapshot

<img width="214" alt="Screen Shot 2022-04-10 at 11 31 19 PM" src="https://user-images.githubusercontent.com/97269758/162627118-ad7bac7d-9451-4fae-a8f5-7e6d97c0141c.png">




