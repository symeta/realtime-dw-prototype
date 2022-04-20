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

sudo docker build . -t {ECR_Repository}/connect-debezium

sudo docker push {ECR_Repository}/connect-debezium
```

We have built the docker image of this connetor in aws ecr. Now, we are going to deploy the image in a pod @EKS cluster.

```sh
kubectl apply -f debezium-mysql-connector.yaml
```
the debezium-mysql-connector.yaml file template could be acquired through the attachement of this blog.


