# Kafka Client Commands

## Create a Topic

```sh
./kafka-topics.sh --create --topic <kafka topic name> --bootstrap-server <MSK bootstrap server endpoints> --partitions <partition number> --replication-factor <replication factor number>
```

## Consume a Topic

```sh
./kafka-console-consumer.sh --bootstrap-server <MSK bootstrap server endpoints> --topic <kafka topic name>
```

For more information, please visit:

https://betterprogramming.pub/kafka-cli-commands-1a135a4ae1bd

