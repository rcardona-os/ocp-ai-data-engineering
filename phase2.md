# Openshift AI Data Engineering

## Phase 2: Platform & Storage Initialization

At this stage the focus shifts from the messaging layer to the "brain" of the operation: **Red Hat OpenShift AI (RHOAI)**. Next step is to set up the environment where the Data Scientists will build their Elyra DAGs and where the Argo engine will execute them.

Steps to get Phase 2 rolling:

1. Verify the RHOAI Operator
Before we can create pipelines, we need the platform itself.

Go to the OpenShift Web Console.

Navigate to Operators > Installed Operators.

Check if Red Hat OpenShift AI is installed. (Make sure you switch the project dropdown back to All namespaces to see cluster-wide operators).

![Installed Operators](media/installed-operators.png)

2. Initialize the AI Platform

  1.  Look for the **Data Science Cluster** tab, and click **Create Instance**.

  ![creating-dsc.png](media/creating-dsc-cluster.png)

  2.  You can keep the default name (usually `default-dsc`).

---

#### Crucial Step:
In the configuration (Form or YAML), ensure the following components are set to **Managed**:

* **dashboard:** (This puts the link in your grid).
* **pipelines:** (Required for your automated ETL orchestration).
* **workbenches:** (For your Jupyter/Elyra environments).
* **distributedworkloads:** (Essential for your Spark processing).

Example yaml definition
```yaml

```

-Expeceted:

![Expeceted](media/installed-operators.png)

3. Prepare your S3 Credentials
As outlined in your project requirements, Data Science Pipelines require an S3-compatible object store to save run logs and artifacts. You will need:

- S3 Endpoint URL (e.g., AWS S3 or MinIO)

- Access Key ID

- Secret Access Key

- Bucket Name (e.g., osf-pipeline-artifacts)
