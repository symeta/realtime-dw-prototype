# Provision MSK cluster

Choose "Quick create" to create the cluster is enough. By default, the broker configuration is set to be kafka.m5.large (2vCPU, 8GiB Memory, 100Gib EBS storage). the maximium IO such configuration can take, by experience, is 15-25MB/s per broker. This indicator is just for evaluation, for production usage, it is essential to do a test on the IO.

<img width="843" alt="Screen Shot 2022-04-10 at 10 16 22 PM" src="https://user-images.githubusercontent.com/97269758/162623652-b553a8ba-8a35-4ba2-a21e-57c161d9a515.png">

In order to have the kafka topics auto-created mapping the tables in mysql database (in this case, is salesdb), we need to create custome configuration for the MSK cluster.

<img width="1428" alt="Screen Shot 2022-04-10 at 10 18 38 PM" src="https://user-images.githubusercontent.com/97269758/162623780-dc7f185f-2ec7-4fa3-a358-18304e244901.png">

Add the following command to in the code session of the configration creation console 
```vim
auto.create.topics.enable=true
```

