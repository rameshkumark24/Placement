# # MLflow — Industrial Cheat Sheet (Beginner → Expert)

---

## Quick index

1. Install & Quickstart
2. Concepts & URIs
3. Local dev workflow (tracking API + autolog)
4. MLproject & Projects runs
5. Models: save, load, flavors
6. Tracking Server (single-node → prod)
7. Backend store & Artifact store choices
8. CLI & REST examples
9. Model Registry (register, transition, stage)
10. Serving & Deployment (local, Docker, SageMaker, Azure)
11. CI/CD & Promotion patterns
12. Security, HA, scaling, infra tips
13. Best practices & troubleshooting snippets
14. Sample docker-compose (tracking server + Postgres + MinIO)

---

## 1. Install & Quickstart

Install MLflow:

```bash
pip install mlflow       # Latest stable MLflow (works for most)
# or for extra deps:
pip install "mlflow[s3]==<version>" "mlflow[extras]" 
```

Quick local run:

```python
# simple_tracking.py
import mlflow
mlflow.set_experiment("quick-exp")
with mlflow.start_run():
    mlflow.log_param("p", 5)
    mlflow.log_metric("m", 0.92)
    with open("out.txt","w") as f: f.write("hello")
    mlflow.log_artifact("out.txt")
```

Run:

```bash
python simple_tracking.py
mlflow ui --port 5000    # starts UI at http://127.0.0.1:5000
```

---

## 2. Core Concepts & URI formats

* **Experiment**: logical group of runs.
* **Run**: single execution (params, metrics, artifacts).
* **Model (flavor)**: saved model with metadata + environment.
* **Registry**: central model store with versions & stages.

Common URIs:

* `file:///path/to/artifacts` — local artifact root
* `sqlite:///mlflow.db` — local SQLite backend store
* `postgresql://user:pw@host:5432/mlflow_db` — Postgres backend store
* `s3://bucket/path` — artifact store (S3/MinIO)
* `runs:/<run_id>/model` — reference to a run artifact model
* `models:/<model_name>/<stage>` — model registry reference

---

## 3. Tracking API — Basic & Advanced

### Basic API

```python
import mlflow
mlflow.set_experiment("exp1")

with mlflow.start_run(run_name="run1") as run:
    mlflow.log_param("lr", 0.01)
    mlflow.log_metric("auc", 0.92)
    mlflow.log_artifact("train.log")
    run_id = run.info.run_id
```

### Client API

```python
from mlflow.tracking import MlflowClient
client = MlflowClient(tracking_uri="http://my-tracking:5000")
runs = client.search_runs(experiment_ids=["1"], filter_string="metrics.auc > 0.9")
client.set_tag(run_id, "team", "fraud")
```

### Autologging (Frameworks)

```python
import mlflow.sklearn
mlflow.sklearn.autolog()   # before training code
# Keras / Tensorflow
import mlflow.keras
mlflow.keras.autolog()
# PyTorch
import mlflow.pytorch
mlflow.pytorch.autolog()
```

### Log model programmatically

```python
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier()
model.fit(X_train, y_train)

mlflow.sklearn.log_model(model, "rf-model", registered_model_name="RF-Model") 
# this will log to artifacts and optionally register if registry is enabled
```

---

## 4. MLproject & MLflow Projects

`MLproject` — reproducible runs:

```yaml
name: my-project
conda_env: conda.yaml
entry_points:
  main:
    parameters:
      alpha: {type: float, default: 0.5}
    command: "python train.py --alpha {alpha}"
```

Run project:

```bash
mlflow run . -P alpha=0.1            # local run with conda env (or virtualenv)
mlflow run git@github.com:org/repo -P param=1.0  # run remote project
```

`conda.yaml` example:

```yaml
name: mlflow-env
channels:
  - defaults
dependencies:
  - python=3.10
  - scikit-learn=1.2
  - pip:
    - mlflow
    - pandas
```

---

## 5. Models: Save, Load, Flavors

### Flavors

* `python_function` (pyfunc) — universal
* `mlflow.sklearn` — sklearn model
* `mlflow.pytorch` — pytorch
* `mlflow.keras` — keras/tf
* `mlflow.xgboost` — xgboost
* `mlflow.spark` — spark

### Save + Load

```python
# Save
mlflow.pyfunc.save_model(path="models/pyfunc_model", python_model=my_pyfunc_model)

# Log model to tracking
mlflow.pyfunc.log_model("pyfunc_model", python_model=my_pyfunc)

# Load
loaded = mlflow.pyfunc.load_model("runs:/<run_id>/pyfunc_model")
preds = loaded.predict(pd.DataFrame(X_test))
```

### Model signature & input_example

```python
from mlflow.models.signature import infer_signature
signature = infer_signature(X_train, model.predict(X_train))
mlflow.sklearn.log_model(model, "model", signature=signature, input_example=X_train[:5])
```

---

## 6. Tracking Server: Local → Production

### Quick UI

```bash
mlflow ui --backend-store-uri sqlite:///mlflow.db --default-artifact-root file:///tmp/mlruns --host 0.0.0.0 --port 5000
```

### Production tracking server (single node)

Use a DB backend and a remote artifact store:

```bash
mlflow server \
  --backend-store-uri postgresql://user:pw@postgres:5432/mlflow_db \
  --default-artifact-root s3://mlflow-artifacts/ \
  --host 0.0.0.0 --port 5000
```

Wrap with `gunicorn` for better concurrency:

```bash
gunicorn --bind 0.0.0.0:5000 --workers 4 "mlflow.server:app"
```

Behind nginx / TLS:

* Terminate TLS at nginx or load balancer
* Proxy pass to gunicorn

### Start with SQLite (dev)

```bash
# start a UI with local sqlite + file artifacts
mlflow server --backend-store-uri sqlite:///mlflow.db --default-artifact-root file:///opt/mlflow/artifacts
```

---

## 7. Backend store & Artifact store — choices & env vars

### Backend (metadata)

* SQLite (dev only)
* Postgres / MySQL (prod) — recommended Postgres
* Databases need proper migrations handled by MLflow on first run.

### Artifact store (actual files)

* S3 / MinIO (`s3://bucket/path`)
* GCS (`gs://bucket/path`)
* Azure Blob (`wasbs://container@account.blob.core.windows.net`)
* NFS / shared filesystem (`file:///mnt/artifacts`)

### S3 env vars example

```bash
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
export AWS_REGION=ap-south-1
```

For MinIO:

```bash
export AWS_ACCESS_KEY_ID=minioadmin
export AWS_SECRET_ACCESS_KEY=minioadmin
export MLFLOW_S3_ENDPOINT_URL=http://minio:9000
```

Set MLflow server env to accept S3 custom endpoint:

```bash
export MLFLOW_S3_ENDPOINT_URL=http://minio:9000
```

---

## 8. CLI & REST examples

### CLI

```bash
# List experiments
mlflow experiments list

# Create experiment
mlflow experiments create --experiment-name "exp1"

# Run a project
mlflow run . -P lr=0.01

# Serve a model locally (pyfunc)
mlflow models serve -m runs:/<run_id>/model -p 1234 --no-conda

# Serve models as a Docker container
mlflow models build-docker -m runs:/<run_id>/model -n mymodel:latest

# Register a model via CLI
mlflow models serve -m models:/MyModel/Production
```

### REST examples (create run, log metric)

```bash
# Create run
curl -X POST -H "Content-Type: application/json" \
  -d '{"experiment_id":"1","start_time":<ts>}' \
  http://mlflow-server:5000/api/2.0/preview/mlflow/runs/create

# Log metric
curl -X POST -H "Content-Type: application/json" \
  -d '{"run_id":"<run_id>","key":"accuracy","value":0.95,"timestamp":<ts>}' \
  http://mlflow-server:5000/api/2.0/preview/mlflow/runs/log-metric
```

### REST Registry (register model)

```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"name": "MyModel", "source": "runs:/<run_id>/model"}' \
  http://mlflow-server:5000/api/2.0/mlflow/registry/register-model
```

> Use official MLflow REST API docs for full payload details (URL path `/api/2.0/mlflow/...`).

---

## 9. Model Registry — register & manage

### Register model (Python)

```python
from mlflow.tracking import MlflowClient
client = MlflowClient()
result = client.create_registered_model("MyModel")
mv = client.create_model_version("MyModel", "runs:/<run_id>/model", run_id)
```

### Transition stages

```python
client.transition_model_version_stage(
    name="MyModel",
    version=1,
    stage="Staging",
    archive_existing_versions=True
)
# Promote to Production
client.transition_model_version_stage("MyModel", 1, "Production")
```

### Other operations

```python
client.get_latest_versions("MyModel", stages=["Staging","Production"])
client.search_model_versions("name='MyModel'")
client.update_model_version(...)
client.delete_model_version("MyModel", 1)
client.delete_registered_model("MyModel")
```

---

## 10. Serving & Deployment Options

### Local Serve (simple)

```bash
mlflow models serve -m runs:/<run_id>/model -p 1234 --no-conda
# test
curl -d '{"columns":["x1","x2"],"data":[[1,2]]}' -H 'Content-Type:application/json' localhost:1234/invocations
```

### Docker

```bash
mlflow models build-docker -m runs:/<run_id>/model -n myimage:latest
docker run -p 1234:8080 myimage:latest
```

### SageMaker (AWS)

```bash
mlflow sagemaker build-and-push-container -m runs:/<run_id>/model -r <aws-account-id>.dkr.ecr.<region>.amazonaws.com/mlflow
mlflow sagemaker deploy -m runs:/<run_id>/model -i <ecr-image> -c <config> --region <region> -n endpoint-name
```

### Azure ML

```bash
mlflow azureml build-and-deploy -m runs:/<run_id>/model -w <workspace> -r <resource_group>
```

### Kubernetes (kubeflow/helm)

* Build Docker image (previous step)
* Create k8s deployment + autoscaling + ingress
* Use readiness/liveness probes
* Use horizontal autoscaler based on CPU / custom metrics

### MLflow Deployments (plugin based)

```bash
mlflow deployments create --target azureml --name mydeploy --flavor pytorch --model-uri runs:/<run_id>/model
```

(Requires provider plugins configured)

---

## 11. CI/CD & Model Promotion Patterns

Example GitHub Actions flow:

1. Run tests & lint
2. Run reproducible `mlflow run` or `python -m pytest`
3. If metrics pass, **log model** and **register** model via `MlflowClient.create_model_version`
4. Open PR → Automated review → after approvals, call `transition_model_version_stage` to `Staging`
5. Run smoke tests in staging endpoint (integration tests)
6. After approvals, transition to `Production`.

Recommended gating:

* Metric thresholds (e.g., AUC > 0.9)
* Manual approval step before Production transition
* Canary deploy + shadow traffic

Example promotion script (Python):

```python
client = MlflowClient()
client.create_model_version("ModelA", "runs:/<run_id>/model", run_id)
# wait/validate then:
client.transition_model_version_stage("ModelA", version, "Staging")
```

---

## 12. Security, HA, Scaling, Infra Tips

### Security

* Run MLflow server behind HTTPS (nginx/ALB)
* Use auth layer (SSO, OAuth) at proxy level
* For registry actions, use API tokens & RBAC at app layer
* Use cloud IAM for artifact store (S3/GCS)

### High Availability

* Use a managed DB (Postgres) with backups
* Use S3/GCS for artifacts (durable)
* Run multiple gunicorn workers behind a load balancer
* Use autoscaling for serving containers

### Scaling

* For heavy artifact I/O, use S3 + CDN
* Offload model serving to dedicated inference infra (KFServing, SageMaker)
* Use asynchronous logging for high-throughput experiments

---

## 13. Best Practices & Production Checklist

* **Separate dev/staging/prod** tracking servers (different experiment namespaces)
* **Use Postgres** (not SQLite) for prod backend store
* **Use S3/GCS/Azure Blob** for artifact store (not local fs)
* **Use registered models** for deployments (models:/MyModel/Production)
* **Add model signature & input_example** for safe serving
* **Set random seeds** and log them
* **Log conda/requirements** with `mlflow.log_artifact("conda.yaml")` or `mlflow.pytorch.log_model(..., conda_env=...)`
* **Tag runs** with `client.set_tag(run_id, "git_sha", sha)`
* **Log source version**: `mlflow.set_tag("git_sha", commit_hash)`
* **Keep experiments small & tidy** — cleanup old runs
* **Enable autolog** for quick reproducibility
* **Use experiments as namespaces** for teams/projects
* **Use lifecycle stages**: Staging → Production with manual approvals
* **Unit + Integration tests** for model inference

---

## 14. Troubleshooting Snippets

### View runs by filter:

```python
client.search_runs(["1"], filter_string="metrics.accuracy >= 0.9", order_by=["metrics.accuracy DESC"])
```

### Get artifact URI:

```python
run = client.get_run(run_id)
print(run.info.artifact_uri)   # e.g. s3://bucket/...
```

### Download artifact:

```python
mlflow.artifacts.download_artifacts(run_id=run_id, path="model")
# or CLI:
mlflow artifacts download -r <run_id> -p model
```

### Clean experiments:

```bash
# Delete experiment (archive)
mlflow experiments delete --experiment-id 1
# Permanently delete runs via API (careful)
```

---

## 15. Example docker-compose (tracking server + Postgres + MinIO)

Save as `docker-compose.yml` (dev / small-team setup)

```yaml
version: '3.7'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: mlflow
      POSTGRES_PASSWORD: mlflow
      POSTGRES_DB: mlflow_db
    volumes:
      - ./pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  minio:
    image: minio/minio
    command: server /data
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    ports:
      - "9000:9000"
    volumes:
      - ./minio_data:/data

  mlflow:
    image: python:3.10-slim
    depends_on:
      - postgres
      - minio
    volumes:
      - ./mlflow_artifacts:/mlflow/artifacts
      - ./mlflow_logs:/mlflow/logs
    environment:
      AWS_ACCESS_KEY_ID: minioadmin
      AWS_SECRET_ACCESS_KEY: minioadmin
      MLFLOW_S3_ENDPOINT_URL: http://minio:9000
    command: >
      bash -c "
      pip install mlflow psycopg2-binary boto3 &&
      mlflow server \
        --backend-store-uri postgresql://mlflow:mlflow@postgres:5432/mlflow_db \
        --default-artifact-root s3://mlflow/ \
        --host 0.0.0.0 --port 5000
      "
    ports:
      - "5000:5000"
```

Then:

```bash
docker-compose up -d
# Access MLflow UI at http://localhost:5000
# Access MinIO at http://localhost:9000 (minioadmin/minioadmin)
```

---

## 16. Useful Patterns & Code Recipes

### Log code + git sha with run

```python
import subprocess, mlflow
sha = subprocess.check_output(["git","rev-parse","HEAD"]).decode().strip()
mlflow.set_tag("git_sha", sha)
mlflow.log_artifact("train.py")
```

### Log pip requirements automatically

```bash
pip freeze > requirements.txt
# in run:
mlflow.log_artifact("requirements.txt")
```

### Autolog + manual log of hyperparams

```python
mlflow.sklearn.autolog()
with mlflow.start_run():
    mlflow.log_param("seed", 42)
    model.fit(X_train, y_train)
```

### Load registered production model for inference

```python
model = mlflow.pyfunc.load_model("models:/MyModel/Production")
pred = model.predict(df_input)
```

### Use model signature to validate input

```python
from mlflow.models import infer_signature
signature = infer_signature(X_train, model.predict(X_train))
mlflow.sklearn.log_model(model, "model", signature=signature, input_example=X_train.head(2))
```

---

## 17. Advanced: Multi-tenant & Multi-environment tips

* Use **experiment per team** or prefix `team/project/exp`
* Use DB schemas or separate DBs for different tenants
* Use S3 prefixes `s3://mlflow/{team}/{project}/artifacts`
* Restrict registry permissions via a fronting service (no built-in RBAC)
* Use proxies + OAuth2 SSO (AuthN / AuthZ) in front of MLflow UI

---

## 18. Quick Reference CLI Commands

```bash
# Start UI
mlflow ui --backend-store-uri sqlite:///mlflow.db --default-artifact-root file:///tmp/artifacts

# Run project
mlflow run . -P n_estimators=100

# List experiments
mlflow experiments list

# List runs of experiment id 1
mlflow runs list -e 1

# Serve model
mlflow models serve -m runs:/<run_id>/model -p 1234 --no-conda

# Build docker image for model
mlflow models build-docker -m runs:/<run_id>/model -n mymodel:v1

# Download artifact
mlflow artifacts download -r <run_id> -p model

# Log a parameter locally (interactive)
python -c "import mlflow; mlflow.log_param('test',1)"
```

---

## 19. Common Interview / Expert Questions (and short answers you should give)

* **Q:** Why use Postgres for backend store?
  **A:** Production-grade, concurrent writes, reliability, backups, ACID compliance.

* **Q:** Why S3 for artifacts?
  **A:** Durable, scalable, accessible by multiple services and nodes.

* **Q:** How do you avoid data leakage in MLflow experiments?
  **A:** Keep train/test split prior to logging artifacts, use pipelines, log preprocessing code, tag runs, avoid fitting transformers on full dataset.

* **Q:** How to rollback a model?
  **A:** Use model registry to transition previous version back to `Production` (archive current prod if desired).

* **Q:** How to handle secret keys?
  **A:** Use vault/secret manager (AWS Secrets Manager / Azure KeyVault) and inject via env or runtime secrets — do not store in code or artifacts.

---

## 20. Final Checklist for Productionizing MLflow

* Backend store: Postgres (managed)
* Artifact store: S3 / GCS / Azure Blob
* Use HTTPS + proxy + auth
* Run MLflow app with gunicorn + LB (not bare mlflow server)
* Automate model registration & promotion via CI/CD with approvals
* Serve models in dedicated infra (K8s / SageMaker) with autoscaling
* Log metadata: git_sha, dataset hash, conda env, seed, run tags
* Enforce input schemas via model signature and validation layer
* Create run cleanup & lifecycle policies (archive old runs)
* Monitor usage, costs (S3, compute) and access logs
