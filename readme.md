# spark-CZE

## Pending Items
TODO: Unit & Integration Tests.

**updated:** May - 2021

## Introduction

This project is aimed to show a simple 2 way ETL

1) load data from csv file to MSSQL
2) query MSSQL, filter and save to csv file

## Prerequisites

a) HPECP 5.3 or K8s with Spark Operator installed

b) (optional) DataFabric or External Storage Solution

c) (optional) sbt, if we want to package the project, else we can just leverage the prebuilt .jar

d) (optional) If you plan to build your jar artifacts, on any machine you can install sbt (scala build tool)
```
curl https://bintray.com/sbt/rpm/rpm | sudo tee /etc/yum.repos.d/bintray-sbt-rpm.repo
sudo yum install sbt -y
```

once you have completed the prerequisites, you now have following environments:

* a) **k-master** = K8's master node where you plan to deploy the demo
* b) (optional)  **any-machine** = Any machine with CentOS or RHEL to build jar artifacts

## Setting up the Demo Index

Set-up Steps:

1. Clone Project

2. (optional) Build Artifacts

3. Customize Yaml Definitions

4. Deploy

## Setting up the Demo Extended

1.
```
git clone https://github.com/DarianHarrison/spark-CZE
```

2. (optional) On **any-machine** where you installed sbt 
note: job should take about 2 - 5 mins to build
```
sbt clean package assembly
...

```
```

...
[success] Total time: 14 s, completed Jun 11, 2020 11:08:50 PM
[info] Strategy 'concat' was applied to 2 files (Run the task at debug level to see details)
[info] Strategy 'discard' was applied to 595 files (Run the task at debug level to see details)
[info] Strategy 'first' was applied to 7071 files (Run the task at debug level to see details)
[success] Total time: 61 s (01:01), completed Jun 11, 2020 11:09:50 PM
```
this should create a fat **xxx-assembly-1.0.0.jar** that can be found in the following directory
```
ls target/scala-2.11/
```

3. On **controller-node** or **any data fabric node**

copy generated jar and sample data to the tenantShare
```
"maprfs:///tenant*********/generic-assembly-1.0.0.jar"
data/in.csv

mkdir for app logs
```


4. Deployment

Before deploying K8's applications, you need to modify each of the following files in 

**spark-CZE/kube/**

* 0.csv-to-csv.yamlaml
* 1.csv-to-db.yaml
* 2.db-to-csv.yaml

fields to modify (lines with commens):

* name
* namespace
* spark.mapr.user.secret
* mainClass
* mainApplicationFile
* arguments

note: we have a pretrained model already on the data fabric, so this step can be skipped
```
kubectl apply -f spark-CZE/kube/0.csv-to-csv.yaml
kubectl logs -f pod/spark-csv-to-csv-driver -n <namespace>
```

## 5. Further reading
* https://spark.apache.org/docs/latest/running-on-kubernetes.html
* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator
