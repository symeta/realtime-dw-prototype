# Validate the connection between MSK cluster and Hudi (MSK consumer via flink@emr)

## access the emr master ec2

```sh
sudo -s

flink-yarn-session -jm 1024 -tm 4096 -s 2  \
-D state.checkpoints.dir=s3://msk-lab-emr-log/flink/checkpoints \
-D state.backend=rocksdb \
-D state.checkpoint-storage=filesystem \
-D execution.checkpointing.interval=60000 \
-D state.checkpoints.num-retained=5 \
-D execution.checkpointing.mode=EXACTLY_ONCE \
-D execution.checkpointing.externalized-checkpoint-retention=RETAIN_ON_CANCELLATION \
-D state.backend.incremental=true \
-D execution.checkpointing.max-concurrent-checkpoints=1 \
-D rest.flamegraph.enabled=true \
-d
```
make sure that the s3 bucket has been created.

```sh
/usr/lib/flink/bin/sql-client.sh -s application_1111
```
the console should look like below:

```sh

                                   ▒▓██▓██▒
                               ▓████▒▒█▓▒▓███▓▒
                            ▓███▓░░        ▒▒▒▓██▒  ▒
                          ░██▒   ▒▒▓▓█▓▓▒░      ▒████
                          ██▒         ░▒▓███▒    ▒█▒█▒
                            ░▓█            ███   ▓░▒██
                              ▓█       ▒▒▒▒▒▓██▓░▒░▓▓█
                            █░ █   ▒▒░       ███▓▓█ ▒█▒▒▒
                            ████░   ▒▓█▓      ██▒▒▒ ▓███▒
                         ░▒█▓▓██       ▓█▒    ▓█▒▓██▓ ░█░
                   ▓░▒▓████▒ ██         ▒█    █▓░▒█▒░▒█▒
                  ███▓░██▓  ▓█           █   █▓ ▒▓█▓▓█▒
                ░██▓  ░█░            █  █▒ ▒█████▓▒ ██▓░▒
               ███░ ░ █░          ▓ ░█ █████▒░░    ░█░▓  ▓░
              ██▓█ ▒▒▓▒          ▓███████▓░       ▒█▒ ▒▓ ▓██▓
           ▒██▓ ▓█ █▓█       ░▒█████▓▓▒░         ██▒▒  █ ▒  ▓█▒
           ▓█▓  ▓█ ██▓ ░▓▓▓▓▓▓▓▒              ▒██▓           ░█▒
           ▓█    █ ▓███▓▒░              ░▓▓▓███▓          ░▒░ ▓█
           ██▓    ██▒    ░▒▓▓███▓▓▓▓▓██████▓▒            ▓███  █
          ▓███▒ ███   ░▓▓▒░░   ░▓████▓░                  ░▒▓▒  █▓
          █▓▒▒▓▓██  ░▒▒░░░▒▒▒▒▓██▓░                            █▓
          ██ ▓░▒█   ▓▓▓▓▒░░  ▒█▓       ▒▓▓██▓    ▓▒          ▒▒▓
          ▓█▓ ▓▒█  █▓░  ░▒▓▓██▒            ░▓█▒   ▒▒▒░▒▒▓█████▒
           ██░ ▓█▒█▒  ▒▓▓▒  ▓█                █░      ░░░░   ░█▒
           ▓█   ▒█▓   ░     █░                ▒█              █▓
            █▓   ██         █░                 ▓▓        ▒█▓▓▓▒█░
             █▓ ░▓██░       ▓▒                  ▓█▓▒░░░▒▓█░    ▒█
              ██   ▓█▓░      ▒                    ░▒█▒██▒      ▓▓
               ▓█▒   ▒█▓▒░                         ▒▒ █▒█▓▒▒░░▒██
                ░██▒    ▒▓▓▒                     ▓██▓▒█▒ ░▓▓▓▓▒█▓
                  ░▓██▒                          ▓░  ▒█▓█  ░░▒▒▒
                      ▒▓▓▓▓▓▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒░░▓▓  ▓░▒█░

    ______ _ _       _       _____  ____  _         _____ _ _            _  BETA
   |  ____| (_)     | |     / ____|/ __ \| |       / ____| (_)          | |
   | |__  | |_ _ __ | | __ | (___ | |  | | |      | |    | |_  ___ _ __ | |_
   |  __| | | | '_ \| |/ /  \___ \| |  | | |      | |    | | |/ _ \ '_ \| __|
   | |    | | | | | |   <   ____) | |__| | |____  | |____| | |  __/ | | | |_
   |_|    |_|_|_| |_|_|\_\ |_____/ \___\_\______|  \_____|_|_|\___|_| |_|\__|

        Welcome! Enter 'HELP;' to list all available commands. 'QUIT;' to exit.


Flink SQL>
```
execute one by one the following command:

```sql
set sql-client.execution.result-mode=tableau;

set 'parallelism.default' = '1';
```

create a flink table as the consumer of kafka topic. the DDL is as below:

```sql

CREATE TABLE tb1 (
id int,
`ts` TIMESTAMP(3) METADATA FROM 'timestamp' ) WITH (
  'connector' = 'kafka',
  'topic' = 'tutorial.salesdb.order',
  'properties.bootstrap.servers' = 'b-2.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092,b-1.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092,b-3.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092',
  'properties.group.id' = 'test-group-001',
  'scan.startup.mode' = 'latest-offset',
  'format' = 'debezium-json',
  'debezium-json.schema-include' = 'true' 
);
```

insert another 2 records into mysql table:
```sql
insert into `order` (`id`) values('22');
insert into `order` (`id`) values('23');
```

observe from the fink terminal 

```sql
Flink SQL> select * from tb1;
2022-04-11 15:01:53,902 INFO  org.apache.hadoop.yarn.client.RMProxy                        [] - Connecting to ResourceManager at ip-172-31-24-70.cn-north-1.compute.internal/172.31.24.70:8032
2022-04-11 15:01:54,107 INFO  org.apache.hadoop.yarn.client.AHSProxy                       [] - Connecting to Application History server at ip-172-31-24-70.cn-north-1.compute.internal/172.31.24.70:10200
2022-04-11 15:01:54,116 INFO  org.apache.flink.yarn.YarnClusterDescriptor                  [] - No path for the flink jar passed. Using the location of class org.apache.flink.yarn.YarnClusterDescriptor to locate the jar
2022-04-11 15:01:54,119 WARN  org.apache.flink.yarn.YarnClusterDescriptor                  [] - Neither the HADOOP_CONF_DIR nor the YARN_CONF_DIR environment variable is set.The Flink YARN Client needs one of these to be set to properly load the Hadoop configuration for accessing YARN.
2022-04-11 15:01:54,229 INFO  org.apache.flink.yarn.YarnClusterDescriptor                  [] - Found Web Interface ip-172-31-25-19.cn-north-1.compute.internal:39527 of application 'application_1649556263454_0003'.
+----+-------------+-------------------------+
| op |          id |                      ts |
+----+-------------+-------------------------+
| +I |          22 | 2022-04-11 15:02:11.085 |
| +I |          23 | 2022-04-11 15:02:17.102 |
```
