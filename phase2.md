# Openshift AI Data Engineering

## Phase 2: Platform & Storage Initialization

At this stage the focus shifts from the messaging layer to the "brain" of the operation: **Red Hat OpenShift AI (RHOAI)**. Next step is to set up the environment where the Data Scientists will build their Elyra DAGs and where the Argo engine will execute them.

Steps to get Phase 2 rolling:

1. `Enabling Kserve - OpenShift Service Mesh 3`

   ### Step 1: Deploy the Istio CNI (Container Network Interface)
   In Mesh 3, the **CNI** must be installed first because it handles network interception.

   1.  Go to the top-left **Project** dropdown and click **Create Project**.
   2.  Name it exactly `istio-cni` and click **Create**.
   3.  Go to **Operators** -> **Installed Operators** and click the **Red Hat OpenShift Service Mesh** operator.
   4.  Go to the **IstioCNI** tab (not *Istio*) and click **Create IstioCNI**.
   5.  Ensure the name is `default` and the namespace is `istio-cni`, then click **Create**.

  ### Step 2: Deploy the Istio Control Plane
  Now that the CNI is ready, you can deploy the actual routing engine.

  1.  Go back to the **Project** dropdown and click **Create Project**.
  2.  Name it exactly `istio-system` and click **Create**.
  3.  Go to **Operators** -> **Installed Operators** and click the **Service Mesh Operator**.
  4.  This time, go to the **Istio** tab and click **Create Istio**. 
      > **Note:** Notice how it is called *Istio* now, replacing the old `ServiceMeshControlPlane` resource!
  5.  Ensure the namespace at the top says `istio-system`, accept the default YAML/form, and click **Create**.
---

1. Verify the RHOAI Operator. Go to the OpenShift Web Console.

   `Navigate to Ecosystem > Installed Operators`

   Check if Red Hat OpenShift AI is installed.

   ![Installed Operators](media/installed-operators.png)

2. Initialize the AI Platform

    1. Look for the **Data Science Cluster** tab, and click **Create DataScienceCluster**.

    ![creating-dsc.png](media/creating-dsc-cluster.png)

    2. It can keep the default name (usually `default-dsc`).

#### Crucial Step:
   In the configuration (Form or YAML), ensure the following components are set to **Managed**:

   * **dashboard:** (This puts the link in your grid).
   * **datasciencepipelines:** (Required for your automated ETL orchestration).
   * **workbenches:** (For your Jupyter/Elyra environments).
   * **distributed workloads (CodeFlare & Ray):** (Essential for your Spark processing).

  Example yaml definition
  ```yaml
  kind: DataScienceCluster
  apiVersion: datasciencecluster.opendatahub.io/v1
  metadata:
    name: default-dsc
    labels:
      app.kubernetes.io/name: datasciencecluster
      app.kubernetes.io/instance: default-dsc
      app.kubernetes.io/part-of: rhods-operator
      app.kubernetes.io/managed-by: kustomize
      app.kubernetes.io/created-by: rhods-operator
  spec:
    components:
      codeflare:
        managementState: Managed
      dashboard:
        managementState: Managed
      datasciencepipelines:
        managementState: Managed
      feastoperator:
        managementState: Removed
      kserve:
        managementState: Managed
        serving:
          ingressGateway:
            certificate:
              type: OpenshiftDefaultIngress
          managementState: Managed
          name: knative-serving
      llamastackoperator:
        managementState: Removed
      kueue:
        managementState: Managed
      modelmeshserving:
        managementState: Managed
      modelregistry:
        managementState: Managed
        registriesNamespace: rhoai-model-registries
      ray:
        managementState: Managed
      workbenches:
        managementState: Managed
      trainingoperator:
        managementState: Managed
      trustyai:
        managementState: Managed
  ```

-Expeceted (after ~5 min):

![Expeceted](media/running-dsc.png)

3. Prepare your S3 Credentials
As outlined in your project requirements, Data Science Pipelines require an S3-compatible object store to save run logs and artifacts. You will need:

- S3 Endpoint URL (e.g., AWS S3 or MinIO)

- Access Key ID

- Secret Access Key

- Bucket Name (e.g., osf-pipeline-artifacts)
