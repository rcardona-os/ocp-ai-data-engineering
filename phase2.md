# Openshift AI Data Engineering

## Phase 2: Platform & Storage Initialization

Before the data can be processed, it must establish the real-time ingestion layer using **Red Hat AMQ Streams**.

1. **Deploy the Operator**
   Install the **Streams for Apache Kafka** (top left) which is the Cluster Operator. It is the foundation for managing the Kafka brokers, topics, and users.

![](media/kafka-cluster-operator.png)

2. **Provision the Kafka Cluster**
   Define a Kafka custom resource to manage brokers, topics, and users as native OpenShift resources. 
   
   2.1 **Create Project**

   Project Name: osf-data-pipelines

![](media/create-kafka-cluster-project.png)

   Before creating any instances, ensure you are in the correct project. The architectural plan specifies **osf-data-pipelines** for data-related workloads. In the top-left dropdown of your OpenShift console, switch from openshift-operators to osf-data-pipelines.
