# Openshift AI Data Engineering

## Phase 3: Processing & Distributed Workloads

The objective of this phase is to provision a StatefulSet-backed Workbench within a dedicated OpenShift namespace and verify the secure injection of S3 credentials via the Data Connection mechanism. This ensures the environment is primed for S3-based ETL operations and distributed compute orchestration.

## Steps to get Phase 3 rolling:

#### 1. Workbench Provisioning

1. Navigate to the **OpenShift AI Dashboard** -> **Data Science Projects** -> `osf-data-pipelines`.
2. Select the **Workbenches** tab and click **Create workbench**.

![](media/wb0.png)

3. **Image Selection:** Select the *Standard Data Science* or *PyTorch* notebook image.

![](media/wb1.png)
   
   > **Note:** These images include pre-baked CLI tools like `boto3`, `oc`, and `pip`.

4. **Deployment Configuration:** * **Container Size:** Select a resource profile (e.g., *Medium: 1 CPU, 8Gi RAM*).

![](media/wb2.png)

5. **Persistent Storage:** Define a Persistent Volume Claim (PVC) size (minimum **20Gi** recommended for local caching).

6. **Data Connection Association:** * Under the **Data connections** section, select **Use existing data connection**.

![](media/wb4.png)

   * Choose the connection created in Phase 2.
   
   > **Technical Note:** This triggers the Injection Logic, where the Operator maps the Secret's Data keys to the Pod’s environment variables.
 
 - Expected:

 ![](media/wb5.png)


#### 2. Verification of Environment Variable Injection

Once the workbench status transitions to **Running**, perform a technical audit of the container environment:

1. Click **Open** to launch the JupyterLab IDE.
2. Open a **New Terminal** (`File` -> `New` -> `Terminal`).
3. Execute the following command to check for the injected S3 metadata:
```bash
env | grep AWS
```

Success Criteria: The terminal must return the following variables, populated with the values from your S3 Secret:

  - AWS_ACCESS_KEY_ID

  - AWS_SECRET_ACCESS_KEY

  - AWS_S3_ENDPOINT

  - AWS_S3_BUCKET