# Provision MSK cluster

Choose "Custom Create" to create the cluster is enough. By default, the broker configuration is set to be kafka.m5.large (2vCPU, 8GiB Memory, 100Gib EBS storage). the maximium IO such configuration can take, by experience, is 15-25MB/s per broker. This indicator is just for evaluation, for production usage, it is essential to do a test on the IO.

<img width="858" alt="Screen Shot 2022-04-12 at 8 25 21 PM" src="https://user-images.githubusercontent.com/97269758/162962011-7007690e-6f54-4b84-844b-2167ff3508f7.png">

<img width="802" alt="Screen Shot 2022-04-12 at 8 26 43 PM" src="https://user-images.githubusercontent.com/97269758/162962172-00f66bc0-d080-45c1-866d-b30c5aabe450.png">

<img width="822" alt="Screen Shot 2022-04-12 at 8 27 10 PM" src="https://user-images.githubusercontent.com/97269758/162962246-afcd2e68-575f-4c7f-ab52-790f1d5e4e9c.png">

<img width="836" alt="Screen Shot 2022-04-12 at 8 27 43 PM" src="https://user-images.githubusercontent.com/97269758/162962339-de5ee622-6d3d-4afa-b369-0bbd9a56ca41.png">

make sure that the VPC is the same with the other resources.
<img width="813" alt="Screen Shot 2022-04-12 at 8 28 28 PM" src="https://user-images.githubusercontent.com/97269758/162962456-d33ff108-c29c-4f7f-ba21-7165a06b4f47.png">

<img width="833" alt="Screen Shot 2022-04-12 at 8 29 37 PM" src="https://user-images.githubusercontent.com/97269758/162962653-7a2ec9e6-74a5-44a7-98f6-29f8413fc3a0.png">

the msk-sg is one that allows all traffic ingress/egress.

In order to have the kafka topics auto-created mapping the tables in mysql database (in this case, is salesdb), we need to create custome configuration for the MSK cluster.

<img width="1428" alt="Screen Shot 2022-04-10 at 10 18 38 PM" src="https://user-images.githubusercontent.com/97269758/162623780-dc7f185f-2ec7-4fa3-a358-18304e244901.png">

Add the following command to in the code session of the configration creation console 
```vim
auto.create.topics.enable=true
default.replication.factor=3
min.insync.replicas=2
num.io.threads=8
num.network.threads=5
num.partitions=1
num.replica.fetchers=2
replica.lag.time.max.ms=30000
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
socket.send.buffer.bytes=102400
unclean.leader.election.enable=true
zookeeper.session.timeout.ms=18000
```

