# Validate the connection between MSK cluster and Hudi (MSK consumer via flink@emr)

## Access the emr master node ec2 as root user
Open a terminal as the client for flink

```sh
sudo -s
```
## Create a Flink session

```sh
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
*Make sure that the s3 bucket has been created.*

## Create a Flink applicaiton

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

## Create a flink table as the consumer of kafka topic. the DDL is as below:

```sql

CREATE TABLE tb1 (
id int,
`ts` TIMESTAMP(3) METADATA FROM 'timestamp' ) WITH (
  'connector' = 'kafka',
  'topic' = 'tutorial.salesdb.order',
  'properties.bootstrap.servers' = '<MSK Bootstrap Server Endpoint>',
  'properties.group.id' = 'test-group-001',
  'scan.startup.mode' = 'latest-offset',
  'format' = 'debezium-json',
  'debezium-json.schema-include' = 'true' 
);
```

## Insert another 2 records into mysql table:

```sql
insert into `order` (`id`) values('22');
insert into `order` (`id`) values('23');
```

## Bbserve from the fink terminal 

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

## Create a hudi sink table to write the data into hudi. the DDL of hudi sink table is as below:
```sql
CREATE TABLE flink_hudi_tb_106( 
id int,
ts TIMESTAMP(3),
logday VARCHAR(255),
hh VARCHAR(255)
)PARTITIONED BY (`logday`,`hh`) WITH (
  'connector' = 'hudi',
  'path' = 's3://msk-lab-emr-log/hudi/flink_hudi_tb_106/',
  'table.type' = 'COPY_ON_WRITE',
  'write.precombine.field' = 'ts',
  'write.operation' = 'upsert',
  'hoodie.datasource.write.recordkey.field' = 'id',
  'hive_sync.enable' = 'true',
  'hive_sync.metastore.uris' = 'thrift://<EMR Cluster Master Node Private IP Address>:9083',
  'hive_sync.table' = 'flink_hudi_tb_106',
  'hive_sync.mode' = 'HMS',
  'hive_sync.username' = 'hadoop',
  'hive_sync.partition_fields' = 'logday,hh',
  'hive_sync.partition_extractor_class' = 'org.apache.hudi.hive.MultiPartKeysValueExtractor'
);
```
the DML of transferring the data is as below:

```sql
insert into flink_hudi_tb_106 select id,ts,DATE_FORMAT(CURRENT_TIMESTAMP, 'yyyy- MM-dd') as logday, DATE_FORMAT(CURRENT_TIMESTAMP, 'hh') as hh from tb1;
```



