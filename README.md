# Validate the connection between Mysql Instance and MSK cluster (MSK Producer)

## Run Debezium Server 
Open 1st terminal to make sure that debezium server is running.
```sh
cd debezium-server
sh run.sh
```
the snapshot of server running is shown is below:

<img width="1476" alt="Screen Shot 2022-04-11 at 10 38 57 PM" src="https://user-images.githubusercontent.com/97269758/162763651-29e98d74-fe10-4a0b-b422-63f43515d2fc.png">

<img width="1481" alt="Screen Shot 2022-04-11 at 10 40 09 PM" src="https://user-images.githubusercontent.com/97269758/162763910-c9e2b2f4-dd61-498d-b837-f2a92ebf6b21.png">

## Insert a data record into the target table
Open a 2nd terminal to be a mysql terminal. Insert a record to the target tabble.

```sql
insert into `order` (`id`) values('21');
```
<img width="544" alt="Screen Shot 2022-04-11 at 10 46 45 PM" src="https://user-images.githubusercontent.com/97269758/162765353-ba0cb82a-4306-4cd1-a80b-0b84e3b1eee0.png">


## Observe the change of MSK cluster
Open a 3rd terminal to be a MSK consumer. Note that the 2nd and 3rd terminal should be opened at the same time, and the following command shoud be executed before inserting into mysql table the record.
execute the following command to see wether the change of the mysql table has been cpatureed and stored in MSK
```sh
./kafka-console-consumer.sh --bootstrap-server b-2.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092,b-1.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092,b-3.msk4.umcrq8.c4.kafka.cn-north-1.amazonaws.com.cn:9092 --topic tutorial.salesdb.order
```
the output is as below:
```json
{
   "schema": {
      "type": "struct",
      "fields": [
         {
            "type": "struct",
            "fields": [
               {
                  "type": "int64",
                  "optional": false,
                  "field": "id"
               }
            ],
            "optional": true,
            "name": "tutorial.salesdb.order.Value",
            "field": "before"
         },
         {
            "type": "struct",
            "fields": [
               {
                  "type": "int64",
                  "optional": false,
                  "field": "id"
               }
            ],
            "optional": true,
            "name": "tutorial.salesdb.order.Value",
            "field": "after"
         },
         {
            "type": "struct",
            "fields": [
               {
                  "type": "string",
                  "optional": false,
                  "field": "version"
               },
               {
                  "type": "string",
                  "optional": false,
                  "field": "connector"
               },
               {
                  "type": "string",
                  "optional": false,
                  "field": "name"
               },
               {
                  "type": "int64",
                  "optional": false,
                  "field": "ts_ms"
               },
               {
                  "type": "string",
                  "optional": true,
                  "name": "io.debezium.data.Enum",
                  "version": 1,
                  "parameters": {
                     "allowed": "true,last,false,incremental"
                  },
                  "default": "false",
                  "field": "snapshot"
               },
               {
                  "type": "string",
                  "optional": false,
                  "field": "db"
               },
               {
                  "type": "string",
                  "optional": true,
                  "field": "sequence"
               },
               {
                  "type": "string",
                  "optional": true,
                  "field": "table"
               },
               {
                  "type": "int64",
                  "optional": false,
                  "field": "server_id"
               },
               {
                  "type": "string",
                  "optional": true,
                  "field": "gtid"
               },
               {
                  "type": "string",
                  "optional": false,
                  "field": "file"
               },
               {
                  "type": "int64",
                  "optional": false,
                  "field": "pos"
               },
               {
                  "type": "int32",
                  "optional": false,
                  "field": "row"
               },
               {
                  "type": "int64",
                  "optional": true,
                  "field": "thread"
               },
               {
                  "type": "string",
                  "optional": true,
                  "field": "query"
               }
            ],
            "optional": false,
            "name": "io.debezium.connector.mysql.Source",
            "field": "source"
         },
         {
            "type": "string",
            "optional": false,
            "field": "op"
         },
         {
            "type": "int64",
            "optional": true,
            "field": "ts_ms"
         },
         {
            "type": "struct",
            "fields": [
               {
                  "type": "string",
                  "optional": false,
                  "field": "id"
               },
               {
                  "type": "int64",
                  "optional": false,
                  "field": "total_order"
               },
               {
                  "type": "int64",
                  "optional": false,
                  "field": "data_collection_order"
               }
            ],
            "optional": true,
            "field": "transaction"
         }
      ],
      "optional": false,
      "name": "tutorial.salesdb.order.Envelope"
   },
   "payload": {
      "before": null,
      "after": {
         "id": 21
      },
      "source": {
         "version": "1.8.1.Final",
         "connector": "mysql",
         "name": "tutorial",
         "ts_ms": 1649687791000,
         "snapshot": "false",
         "db": "salesdb",
         "sequence": null,
         "table": "order",
         "server_id": 2105706617,
         "gtid": null,
         "file": "mysql-bin-changelog.001506",
         "pos": 702,
         "row": 0,
         "thread": null,
         "query": null
      },
      "op": "c",
      "ts_ms": 1649687791502,
      "transaction": null
   }
}
```
