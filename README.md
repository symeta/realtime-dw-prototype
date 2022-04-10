# Provision Mysql Instance

In order that the Mysql instance CDC data can be captured by debezium server, the binlog_format parameter must be set to ROW whereas the default setting is MIXED. Since the managed RDS instance has prohibited altering thoese system parameters from the mysql command line, such operation is done through creating a new parameter group from RDS console. the snapshot is shown as below:

<img width="1629" alt="Screen Shot 2022-04-10 at 8 57 00 PM" src="https://user-images.githubusercontent.com/97269758/162619363-3a3cb433-a544-4fc7-9d08-bfd99b93e51e.png">

the binlog_format parameter is set to ROW. the snapshot is shown as below:

<img width="1277" alt="Screen Shot 2022-04-10 at 9 00 40 PM" src="https://user-images.githubusercontent.com/97269758/162619437-4fa8116e-a4fc-40ec-aad9-c2922acb4873.png">

After defining the parameter group, make sure that this parameter group is hooked with the mysql instance.
Click "Modify" button and change the database parameter group to the one you just defined.

<img width="1015" alt="Screen Shot 2022-04-10 at 9 10 19 PM" src="https://user-images.githubusercontent.com/97269758/162619831-8f38eff4-2a37-42ef-993c-6eebbb25f00a.png">

<img width="774" alt="Screen Shot 2022-04-10 at 9 13 33 PM" src="https://user-images.githubusercontent.com/97269758/162619922-2dca7ca8-22e7-4a38-8630-774506e4970d.png">


Make sure that the security group for the Mysql instance allows 3306 port ingress traffic from specific destinations.
<img width="725" alt="Screen Shot 2022-04-10 at 9 04 07 PM" src="https://user-images.githubusercontent.com/97269758/162619558-f95b1828-c9f9-401d-a28c-d40b457e3d03.png">

Log in the Mysql command line console through the command below:
```sh
mysql -u admin -h <mysql instance endpoint> -p
```

Create a database named as salesdb.
```sql
create database salesdb;
use salesdb;
```
