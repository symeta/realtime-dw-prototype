# Set up Debezium Connector in a pod @EKS cluster

## 1. Set up an EKS cluster
The process of setting up an EKS cluster could be referred to the below link:
- [1.1 Preparation](https://github.com/symeta/eks-amp-amg/tree/preparation)
- [1.2 Create EKS Cluster @global](https://github.com/symeta/eks-amp-amg/tree/create-eks-cluster)
- [1.3 Preparation & Create EKS Cluster @GCR](https://github.com/symeta/eks-amp-amg/tree/preparation-%26-create-eks-cluster-%40gcr)

## 2. Preparation of setting up the Debezium connector
Access any of the EKS cluster node. Make sure that kubectl, eksctl have been installed on this server. Make sure the rest of the operations are all done through this server.

To install Kubectl, refer to:
- https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

To install eksctl, refer to:
- https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

Install Operator Framework:

```sh
kubectl apply -f https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.18.1/crds.yaml

kubectl apply -f https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.18.1/olm.yaml
```

Install Strimzi Apache Kafka Operator:

```sh
kubectl apply -f https://operatorhub.io/install/strimzi-kafka-operator.yaml
```

Check whether the strimzi kafka operator has been successfully installed.

```sh
kubectl get csv -n operators
```
<img width="898" alt="Screen Shot 2022-04-20 at 7 37 02 PM" src="https://user-images.githubusercontent.com/97269758/164222168-a9b1a2a9-3c67-4fd6-9393-d201182324da.png">

```sh
cd /usr/local/bin
```
and install Debezium Mysql Kafka Connector.

```sh
wget https://repo1.maven.org/maven2/io/debezium/debezium-connector-mysql/1.5.0.Final
/debezium-connector-mysql-1.5.0.Final-plugin.tar.gz

tar -zxvf debezium-connector-mysql-1.5.0.Final-plugin.tar.gz
```

Build a docker image of this Debuzium Mysql Kafka Connector. The Dockerfile could be acquired through the attachment of this blog.

login aws ecr:

```sh
aws ecr get-login --no-include-email

sudo docker build . -t <ECR Repository URI>/connect-debezium

sudo docker push <ECR Repository URI>/connect-debezium
```

We have built the docker image of this connetor in aws ecr. Now, we are going to deploy the image in a pod @EKS cluster.

```sh
kubectl create namespace <customer namespace>

kubectl apply -f debezium-mysql-connector.yaml
```
the debezium-mysql-connector.yaml file template could be acquired through the attachement of this blog.

run the following command to check the status of the pod:

```sh
kubectl get pods -n <custom namespace>
```
<img width="712" alt="Screen Shot 2022-04-20 at 8 04 55 PM" src="https://user-images.githubusercontent.com/97269758/164226588-1082d904-e1d2-4073-9e10-7455afc96334.png">

the command to view the logs for possible trouble shooting:

```sh
kubectl logs <specific pod name> -n <custom namespace>
```
Make the services running on pod accessible by forwarding the specific port the service (port 8083) is using to the one of the server

```sh
kubectl port-forward service/debezium-connector-connect-api 8083:8083 -n <custom namespace>
```

<img width="561" alt="Screen Shot 2022-04-20 at 11 17 14 PM" src="https://user-images.githubusercontent.com/97269758/164264668-a26839cc-1b27-430c-ad2f-37b007279467.png">

check the result of the port forwarding for new terminal

```sh
curl localhost:8083/connector-plugins
```

<img width="1596" alt="Screen Shot 2022-04-20 at 11 18 52 PM" src="https://user-images.githubusercontent.com/97269758/164265360-3ba178e9-de6a-40f0-ac3f-5735fa642f07.png">

configure a mysql connector json file, and push the configure to the kafka mysql connector service.
the mysql-connector.json file could be acquired through the attachment of this blog.

```sh
cat mysql-connector.json | curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d @-
```
Check the status of the connector

```sh
curl localhost:8083/connectors/
```

<img width="459" alt="Screen Shot 2022-04-20 at 11 26 52 PM" src="https://user-images.githubusercontent.com/97269758/164266791-0445f98e-2230-4dbe-bce7-e0d285213106.png">





