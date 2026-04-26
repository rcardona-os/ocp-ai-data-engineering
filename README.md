# Openshift AI Data Engineering
## Excutive Summary
This is workshop to deploy a data engineering in Openshift AI

## Scope

* [Create](https://docs.gitlab.com/user/project/repository/web_editor/#create-a-file) or [upload](https://docs.gitlab.com/user/project/repository/web_editor/#upload-a-file) files

## Architecture

DRAW

## Software Requirements

* Openshift Cluster Platform 4.21.8
* Openshift Operators
  - Red Hat OpenShift AI
  - Red Hat OpenShift Serverless
  - Red Hat OpenShift Service Mesh
 
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

#### [Phase 4: Orchestration & Automation](phase4.md)

#### [Phase 5: Unified Observability](phase5.md)
