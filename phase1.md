# Openshift AI Data Engineering

## Phase 1: Real Time Ingestion Layer Setup

Before the data can be processed, it must establish the real-time ingestion layer using **Apache Kafka**. The first to provision the **Kafka Cluster** is the create an Openshift project.

1. **Create Project**

     Project Name: osf-data-pipelines

     ![](media/create-kafka-cluster-project.png)

     💥 Before creating any instances, ensure you are in the correct project. The architectural plan specifies **osf-data-pipelines** for data-related workloads. In the top-left dropdown of your OpenShift console, switch from openshift-operators to osf-data-pipelines.
---
2. **Create Initial Topics**
   Following the implementation plan, it must now define a Kafka custom resource to provision the cluster brokers with persistent storage. Create the `raw-data` and `etl-input` topics to ensure producers and consumers can operate correctly.

   **Kafka Instance Configuration**

   ![](media/create-kafka-instance.png)

   1. **Create Kafka Node Pool** (Current namespace only)

   ```yaml
   kind: KafkaNodePool
   apiVersion: kafka.strimzi.io/v1beta2
   metadata:
     name: dual-role-pool
     namespace: osf-data-pipelines
     labels:
       strimzi.io/cluster: osf-kafka-cluster
   spec:
     replicas: 3
     roles:
       - controller
       - broker
     storage:
       type: persistent-claim
       size: 100Gi
       class: gp3-csi
       deleteClaim: false
   ```

   ![](media/create-kafka-nodepool.png)

   2. **Click Create Instance** on the Kafka tile.

   ```yaml
   apiVersion: kafka.strimzi.io/v1beta2
   kind: Kafka
   metadata:
     name: osf-kafka-cluster
     namespace: osf-data-pipelines
     annotations:
       strimzi.io/node-pools: enabled
       strimzi.io/kraft: enabled
   spec:
     kafka:
       version: 4.1.0
       listeners:
         - name: plain
           port: 9092
           type: internal
           tls: false
         - name: tls
           port: 9093
           type: internal
           tls: true
       config:
         offsets.topic.replication.factor: 3
         transaction.state.log.replication.factor: 3
         transaction.state.log.min.isr: 2
         default.replication.factor: 3
         min.insync.replicas: 2
     entityOperator:
       topicOperator: {}
       userOperator: {}
   ```

     ![](media/create-kafka-cluster.png)

   - Expected:

     ![](media/running-kafka-instance.png)


   3. **Create topics** (Current namespace only)

   - topic: raw-data

   ```yaml
   kind: KafkaTopic
   apiVersion: kafka.strimzi.io/v1beta2
   metadata:
     name: raw-data
     labels:
       strimzi.io/cluster: osf-kafka-cluster
     namespace: osf-data-pipelines
   spec:
     partitions: 3
     replicas: 3
     config:
       retention.ms: 7200000
       segment.bytes: 1073741824
   ```

   - topic: etl

   ```yaml
   kind: KafkaTopic
   apiVersion: kafka.strimzi.io/v1beta2
   metadata:
     name: etl-input
     labels:
       strimzi.io/cluster: osf-kafka-cluster
     namespace: osf-data-pipelines
   spec:
     partitions: 3
     replicas: 3
     config:
       retention.ms: 7200000
       segment.bytes: 1073741824
   ```

   - Expected:

     ![](media/kafka-topics.png)
---

3. **Configure CDC**
   Deploy a Kafka Connect instance (e.g., Debezium) to ingest Change Data Capture events from external databases directly into your Kafka topics.

   ```yaml
   kind: KafkaConnect
   apiVersion: kafka.strimzi.io/v1beta2
   metadata:
     name: osf-connect-cluster
     namespace: osf-data-pipelines
     annotations:
       strimzi.io/use-connector-resources: "true" # Enables managing connectors via YAML
   spec:
     version: 4.1.0
     replicas: 1
     bootstrapServers: osf-kafka-cluster-kafka-bootstrap:9092
     config:
       group.id: connect-cluster
       offset.storage.topic: connect-cluster-offsets
       config.storage.topic: connect-cluster-configs
       status.storage.topic: connect-cluster-status
       config.storage.replication.factor: 3
       offset.storage.replication.factor: 3
       status.storage.replication.factor: 3
     # In a production scenario, you would add 'build' config here 
     # to include Debezium plugins
   ```
   
   - Expected:

   ![](media/kafka-connect.png)

#### [NEXT => Phase 2: Platform & Storage Initialization](phase2.md)
