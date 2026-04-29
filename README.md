# Openshift AI Data Engineering
## Excutive Summary
The objective of this workshop is to bridge the gap between high-speed data ingestion and heavy-duty machine learning workflows. By integrating real-time event streaming (Apache Kafka) with distributed object storage, and automated pipeline orchestration (Kubeflow), we provide data science teams with a "zero-trust," GitOps-ready environment.

This architecture delivers a production-grade Data Engineering and MLOps platform natively on Red Hat OpenShift AI. It bridges the gap between high-velocity event streaming (Kafka) and heavy-duty machine learning workflows (Kubeflow).

The core directive of this design is to abandon "click-ops" and buggy UI wrappers in favor of pure Pipeline-as-Code. By using the Kubeflow Python SDK, the platform ensures that all ML workflows are 100% reproducible, GitOps-ready, and highly secure—relying entirely on native Kubernetes Secret injection to eliminate plain-text credentials.

## Scope

### **Phase 1: Real-Time Ingestion (Kafka)**
* **The Goal:** Handle high-velocity data and event routing.
* **Tech Specs:** * Deploy Apache Kafka to manage live telemetry streams.
  * Set up the foundation for event-driven architecture (e.g., triggering a pipeline run automatically when 10,000 new records arrive or data drift is detected).

### **Phase 2: Platform & Storage (S3)**
* **The Goal:** Establish a decoupled, scalable data lake.
* **Tech Specs:** * Provision S3-compatible object storage for raw data, processed features, and model artifacts.
  * Integrate Cloud Object Storage credentials securely via Kubernetes Secrets.
  * Mount data connections directly to Jupyter Workbenches for exploratory data analysis (EDA) without exposing hardcoded keys.

### **Phase 3: Distributed Processing (Spark)**
* **The Goal:** Heavy-lifting data transformation.
* **Tech Specs:** * Integrate distributed frameworks like Apache Spark within OpenShift AI.
  * Automate the batching of live Kafka streams into S3 (e.g., Parquet files) so downstream ML pipelines can consume the data efficiently.

### **Phase 4: Pipeline Orchestration (Kubeflow Pipelines)**
* **The Goal:** Automated, zero-trust ML model training and deployment.
* **Tech Specs:** * Build Directed Acyclic Graphs (DAGs) using the KFP Python SDK.
  * Dynamically inject Kubernetes environment variables into pipeline containers at runtime to securely access S3.
  * Allocate hardware (CPUs/GPUs) on the fly per pipeline step.
  * *Explicitly Out-of-Scope:* Visual pipeline editors (like Elyra), which introduce security vulnerabilities with local S3 credential handling.

### **Phase 5: Unified Observability (Grafana)**
* **The Goal:** Track system health and model accuracy.
* **Tech Specs:** * Deploy Grafana dashboards to monitor cluster hardware utilization (GPUs/Memory).
  * Track pipeline execution success rates, duration, and Kafka consumer lag.
  * Lay the groundwork for tracking model data drift and real-time inference latency.

## Architecture

TO BE ADDED

## Software Requirements

* Openshift Cluster Platform 4.21.8
* Openshift Operators
  - Red Hat OpenShift AI
  - Red Hat OpenShift Serverless
  - Red Hat OpenShift Service Mesh
  - Stream for Apache Kafka
 
  ![Installed Operators](media/installed-operators.png)

## Used Infrastructure 

- Control Plane

  - Nodes: 3 x Master Nodes (Standard for HA).

  - Instance Type: m6i.xlarge (4 vCPU / 16GB RAM).

  - Storage: 100GB Root EBS volume per node.

- Compute Plane

  - Nodes: 3 x Worker Nodes
  
  - Instance Type: m6i.xlarge (8 vCPU / 32GB RAM).

  - Storage: 100GB Root EBS volume per node.


- Application Subsystems
  
| Component | Estimated Resource Draw | Source / Reference |
| :--- | :--- | :--- |
| **RHOAI Operators** | ~2 vCPU / 8GB RAM (Idle) | Platform Overhead |
| **Kafka (AMQ Streams)** | ~2 vCPU / 8GB RAM (3 Brokers) | Persistent storage required |
| **Spark Driver** | 4GB RAM (Single instance) | Spec: `spark.driver.memory: 4g` |
| **Spark Executors** | 4 Instances (~2 vCPU / 8GB RAM each) | Spec: `spark.executor.instances: 4` |
| **Jupyter Workbenches** | ~1 vCPU / 2GB RAM per active user | Standard user allocation |

## Building Blocks

#### [Phase 1: Real Time Ingestion Layer Setup](phase1.md)

#### [Phase 2: Platform & Storage Initialization](phase2.md)

#### [Phase 3: Processing & Distributed Workloads](phase3.md)

#### [Phase 4: Pipeline Orchestration & Automation](phase4.md)

#### [Phase 5: Unified Observability](phase5.md)
