# Provision Mysql Instance

In order that the Mysql instance CDC data can be captured by debezium server, the binlog_format parameter must be set to ROW whereas the default setting is MIXED. Since the managed RDS instance has prohibited altering thoese system parameters from the mysql command line, such operation is done through creating a new parameter group from RDS console. the snapshot is shown as below:

<img width="1629" alt="Screen Shot 2022-04-10 at 8 57 00 PM" src="https://user-images.githubusercontent.com/97269758/162619363-3a3cb433-a544-4fc7-9d08-bfd99b93e51e.png">

the binlog_format parameter is set to ROW. the snapshot is shown as below:

<img width="1277" alt="Screen Shot 2022-04-10 at 9 00 40 PM" src="https://user-images.githubusercontent.com/97269758/162619437-4fa8116e-a4fc-40ec-aad9-c2922acb4873.png">
