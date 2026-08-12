# 📘 **XGBOOST FULL NOTES (BEGINNER → INDUSTRIAL LEVEL)**

*(eXtream Gradient Boosting)*

---

# 🟦 **1. What is XGBoost? (Simple Explanation)**

XGBoost = **Extreme Gradient Boosting**
It is the **fastest + most accurate + most used boosting algorithm** in real-world ML systems.

✔️ Used in Kaggle competitions
✔️ Used in industry for fraud detection, credit scoring, churn prediction, recommendations
✔️ Handles missing values automatically
✔️ Extremely fast due to parallelization & optimizations

---

# 🟦 **2. Why XGBoost is so powerful? (Key Features)**

### 🔥 **Main Advantages**

| Feature                            | Explanation                                        |
| ---------------------------------- | -------------------------------------------------- |
| **Regularization**                 | L1 + L2 reduce overfitting (Δ over normal GBM).    |
| **Parallel tree building**         | Huge speed boost.                                  |
| **Handling missing values**        | Automatically learns best direction for missing.   |
| **Tree pruning**                   | Bottom-up pruning (max_depth handled efficiently). |
| **Weighted quantile sketch**       | Works with large datasets.                         |
| **Supports distributed computing** | Cloud scale training.                              |

---

# 🟦 **3. XGBoost vs Gradient Boosting**

| Feature        | Gradient Boosting | XGBoost         |
| -------------- | ----------------- | --------------- |
| Speed          | Slow              | 🚀 Very fast    |
| Regularization | Weak              | Strong (L1, L2) |
| Missing values | Not automatic     | Automatic       |
| Parallelism    | No                | Yes             |
| Accuracy       | Good              | Best            |

---

# 🟦 **4. How XGBoost Works? (Concept Flow)**

1️⃣ Start with base prediction (mean for regression, log-odds for classification)
2️⃣ Compute **residual error**
3️⃣ Train **tree to predict residual**
4️⃣ Add tree predictions to the model
5️⃣ Apply **shrinkage (learning rate)**
6️⃣ Apply **regularization penalty**
7️⃣ Repeat until stopping criteria met

📌 **Final prediction = Sum of all tree outputs**

---

# 🟦 **5. XGBoost Mathematically (Industrial Explanation)**

### 🎯 **Objective Function**

[
Obj = \sum_i l(y_i, \hat{y_i}) + \sum_k \Omega(f_k)
]

### 🎯 **Regularization Term**

[
\Omega(f) = \gamma T + \frac{1}{2} \lambda ||w||^2
]

Where:

* **T** = number of leaves
* **γ** = complexity penalty (minimum loss reduction)
* **λ** = L2 regularization
* **w** = leaf weights

---

# 🟦 **6. XGBoost Architecture**

Core components:

### ✔️ **Gradient Calculation**

Uses 1st & 2nd order derivatives (Newton boosting)

### ✔️ **Tree Growth Strategy**

XGBoost uses **depth-wise** (default) or **loss-guided (best-first)** tree growth.

### ✔️ **Handling Missing Values**

Learns best split direction automatically.

### ✔️ **Sparsity Awareness**

Designed for sparse inputs.

---

# 🟦 **7. Installation**

```bash
pip install xgboost
```

---

# 🟦 **8. Basic Python Code (Classification)**

```python
from xgboost import XGBClassifier

model = XGBClassifier()
model.fit(X_train, y_train)

pred = model.predict(X_test)
```

---

# 🟦 **9. Hyperparameters (Explain Like Company Expert)**

## 🔥 A) **Tree Parameters**

| Parameter            | Importance | Explanation                                       |
| -------------------- | ---------- | ------------------------------------------------- |
| **max_depth**        | ⭐⭐⭐⭐⭐      | Controls model complexity; too high → overfitting |
| **min_child_weight** | ⭐⭐⭐⭐       | Minimum sum of instance weight in a leaf          |
| **gamma**            | ⭐⭐⭐        | Minimum loss reduction to create a split          |
| **subsample**        | ⭐⭐⭐⭐       | % of rows sampled for each tree                   |
| **colsample_bytree** | ⭐⭐⭐        | % of columns sampled for each tree                |

---

## 🔥 B) **Boosting Parameters**

| Parameter               | Explanation                          |
| ----------------------- | ------------------------------------ |
| **learning_rate (eta)** | Shrinks contribution of each tree    |
| **n_estimators**        | Number of trees                      |
| **objective**           | Binary, multi-class, regression etc. |
| **booster**             | gbtree / gblinear / dart             |

---

## 🔥 C) **Regularization Parameters**

| Parameter  | Explanation       |
| ---------- | ----------------- |
| **alpha**  | L1 regularization |
| **lambda** | L2 regularization |

---

## 🔥 D) **Other Parameters**

| Parameter            | Purpose                                       |
| -------------------- | --------------------------------------------- |
| **scale_pos_weight** | Must use for imbalanced data (fraud, medical) |
| **eval_metric**      | AUC/Logloss/Error for monitoring              |

---

# 🟦 **10. Best Hyperparameter Settings (Industry)**

### ✔️ For imbalanced problems (fraud detection)

```python
XGBClassifier(
    scale_pos_weight = total_negative / total_positive,
    max_depth = 5,
    subsample = 0.8,
    colsample_bytree = 0.8
)
```

### ✔️ For large datasets

```python
tree_method = 'hist'
```

---

# 🟦 **11. XGBoost For Regression**

```python
from xgboost import XGBRegressor

model = XGBRegressor(
    max_depth=6,
    learning_rate=0.05,
    n_estimators=500
)
model.fit(X_train, y_train)
```

---

# 🟦 **12. Handling Missing Values**

You **do not** need to impute missing values.
XGBoost chooses the best path for missing entries.

---

# 🟦 **13. Feature Importance in XGBoost**

```python
model.feature_importances_
```

Types:

* Gain
* Weight
* Cover
* Total Gain
* Total Cover

---

# 🟦 **14. XGBoost With Early Stopping**

```python
model.fit(
    X_train, y_train,
    eval_set=[(X_test, y_test)],
    eval_metric='auc',
    early_stopping_rounds=20
)
```

---

# 🟦 **15. XGBoost Evaluation Metrics**

### Classification:

* AUC
* Logloss
* Error Rate
* F1 Score

### Regression:

* RMSE
* MAE
* R²

---

# 🟦 **16. Hyperparameter Tuning Grid**

```python
param_grid = {
    "max_depth": [3,4,5,6],
    "learning_rate": [0.01, 0.1, 0.2],
    "n_estimators": [200, 500],
    "subsample": [0.7, 0.8, 1.0],
    "colsample_bytree": [0.7, 0.8, 1.0]
}
```

---

# 🟦 **17. XGBoost + MLflow (Industry Monitoring)**

Track parameters & metrics:

```python
import mlflow

with mlflow.start_run():
    model = XGBClassifier(...)
    model.fit(...)
    mlflow.log_param("max_depth", 5)
    mlflow.log_metric("auc", auc_score)
```

---

# 🟦 **18. Industrial Use Cases of XGBoost**

### ✔️ Fraud Detection

### ✔️ Credit Risk Scoring

### ✔️ Customer Churn Prediction

### ✔️ Demand Forecasting

### ✔️ Recommender Systems

### ✔️ Healthcare diagnosis

### ✔️ NLP classification

---

# 🟦 **19. XGBoost Best Practices (Professional)**

✔ Normalize with **StandardScaler** for linear booster
✔ For trees, scaling not needed
✔ Use **early stopping**
✔ Monitor **AUC** for classification
✔ Use **GPU training**:

```python
tree_method='gpu_hist'
predictor='gpu_predictor'
```

---

# 🟦 **20. XGBoost Interview Questions (Bonus)**

### **1. Why use XGBoost over Random Forest?**

Better accuracy, regularization, boosting > bagging.

### **2. Why use XGBoost over normal Gradient Boosting?**

Optimization + regularization + parallelization.

### **3. How does XGBoost handle missing values?**

Automatically learns best split direction.

### **4. What is shrinkage?**

Learning rate reduces weight of each tree.

### **5. What is DART booster?**

Dropouts meet boosting → prevents overfitting.

---
# XGBoost — Production-Ready Project Template + Interview Coding Problems

Packed, practical, and ready-to-run. Contains:

* A production-ready XGBoost project scaffold (code + config + ops notes)
* Example scripts (training, inference, evaluation, hyperparameter tuning)
* Deploy & monitoring suggestions (Docker, MLflow, CI/CD, serving)
* 15 interview-style coding problems (with solutions / hints)

---

## Table of contents

1. Project structure (recommended)
2. Requirements & quick start
3. Data contract / schema
4. Preprocessing pipeline (code)
5. Train script (production-ready)
6. Hyperparameter tuning script (Optuna example)
7. Evaluation & metrics logging (MLflow)
8. Model serialization + model card
9. Inference / scoring API (FastAPI + Docker)
10. Batch scoring (Spark/Polars note)
11. CI/CD + testing checklist
12. Monitoring & Observability (Prometheus / MLflow + alerts)
13. GPU & distributed training tips
14. Security & privacy considerations
15. Example production config (YAML)
16. Interview coding problems (15) + solutions

---

## 1) Recommended project structure

```
xgboost_project/
├─ data/
│  ├─ raw/
│  └─ processed/
├─ src/
│  ├─ data/
│  │  ├─ make_dataset.py
│  │  └─ schema.py
│  ├─ features/
│  │  └─ preprocess.py
│  ├─ models/
│  │  ├─ train.py
│  │  ├─ predict.py
│  │  └─ tune.py
│  ├─ evaluation/
│  │  └─ eval.py
│  ├─ api/
│  │  └─ inference_app.py
│  └─ utils/
│     ├─ io.py
│     └─ logging.py
├─ notebooks/
├─ tests/
│  ├─ test_preprocess.py
│  └─ test_inference.py
├─ Dockerfile
├─ docker-compose.yml
├─ pyproject.toml / requirements.txt
├─ mlflow/
│  └─ model_registry_config.yml
├─ configs/
│  └─ default.yaml
└─ README.md
```

---

## 2) Requirements & quick start

`requirements.txt` (essentials)

```
xgboost>=1.6.0
pandas
numpy
scikit-learn
optuna
mlflow
fastapi
uvicorn[standard]
pydantic
joblib
pytest
prometheus-client
```

Quick start (dev)

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
# run unit tests
pytest -q
# train locally
python src/models/train.py --config configs/default.yaml
```

---

## 3) Data contract / schema

Define a canonical schema in `src/data/schema.py`. Example:

```python
from pydantic import BaseModel
from typing import Optional

class InputRow(BaseModel):
    customer_id: str
    age: Optional[int]
    gender: Optional[str]
    annual_income: Optional[float]
    signup_date: Optional[str]
    target: Optional[int]  # only for training
```

Store feature types and allowed values. This ensures consistent production behavior.

---

## 4) Preprocessing pipeline (production-ready)

Key points:

* Use deterministic, serializable transforms
* Fit only on training data; persist transformers (sklearn Pipelines)
* Handle missing values & categories robustly (use `handle_unknown='ignore'`)
* Save `feature_order` for inference

`src/features/preprocess.py` (abridged)

```python
import joblib
import pandas as pd
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import OneHotEncoder, StandardScaler

NUM_FEATURES = ["age", "annual_income"]
CAT_FEATURES = ["gender"]

def build_preprocessor():
    num_pipe = Pipeline([
        ("imputer", SimpleImputer(strategy="median")),
        ("scaler", StandardScaler())
    ])
    cat_pipe = Pipeline([
        ("imputer", SimpleImputer(strategy="constant", fill_value="missing")),
        ("ohe", OneHotEncoder(handle_unknown="ignore", sparse=False))
    ])
    preprocessor = ColumnTransformer([
        ("num", num_pipe, NUM_FEATURES),
        ("cat", cat_pipe, CAT_FEATURES)
    ], remainder="drop")
    return preprocessor

def fit_transform_train(df: pd.DataFrame, save_path: str):
    preprocessor = build_preprocessor()
    X = preprocessor.fit_transform(df)
    joblib.dump(preprocessor, save_path)
    return X

def transform_infer(df: pd.DataFrame, preprocessor_path: str):
    preprocessor = joblib.load(preprocessor_path)
    return preprocessor.transform(df)
```

Persist preprocessor (joblib) to model registry or artifact store.

---

## 5) Train script (production-ready)

Key points:

* Use config file (YAML) for hyperparams
* Log params & metrics to MLflow
* Save model artifact along with preprocessing artifacts and feature list

`src/models/train.py` (abridged)

```python
import yaml, joblib, mlflow, os
import xgboost as xgb
from sklearn.model_selection import train_test_split
from sklearn.metrics import roc_auc_score
import pandas as pd
from features.preprocess import fit_transform_train

def load_config(path): ...
def load_data(path): ...

def train(config_path):
    cfg = load_config(config_path)
    df = load_data(cfg['data']['train_csv'])
    y = df[cfg['data']['target_col']]
    Xdf = df.drop(columns=[cfg['data']['target_col']])

    # Preprocess
    preproc_path = os.path.join(cfg['artifacts_dir'], "preprocessor.joblib")
    X = fit_transform_train(Xdf, preproc_path)

    X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=cfg['training']['val_size'], random_state=42)

    dtrain = xgb.DMatrix(X_train, label=y_train)
    dval = xgb.DMatrix(X_val, label=y_val)
    params = cfg['model']['params']

    mlflow.set_experiment(cfg['tracking']['experiment'])
    with mlflow.start_run():
        model = xgb.train(
            params=params,
            dtrain=dtrain,
            num_boost_round=cfg['training']['num_boost_round'],
            evals=[(dval, "val")],
            early_stopping_rounds=cfg['training']['early_stopping_rounds']
        )
        y_pred = model.predict(dval, ntree_limit=model.best_ntree_limit)
        auc = roc_auc_score(y_val, y_pred)
        mlflow.log_metric("val_auc", float(auc))
        # save model
        model_path = os.path.join(cfg['artifacts_dir'], "xgb_model.json")
        model.save_model(model_path)
        mlflow.log_artifact(model_path)
        mlflow.log_artifact(preproc_path)
```

Notes:

* Use `xgb.DMatrix` for speed
* `best_ntree_limit` for inference
* Use `gpu_hist` tree method when GPU available

---

## 6) Hyperparameter tuning script (Optuna)

Key points:

* Use Optuna for Bayesian optimization
* Optimize AUC on validation set
* Save best parameters to YAML & MLflow

`src/models/tune.py` (abridged)

```python
import optuna, xgboost as xgb, mlflow
from sklearn.model_selection import train_test_split
from sklearn.metrics import roc_auc_score

def objective(trial):
    params = {
        'verbosity': 0,
        'objective': 'binary:logistic',
        'tree_method': 'hist',
        'eta': trial.suggest_loguniform('eta', 0.01, 0.3),
        'max_depth': trial.suggest_int('max_depth', 3, 10),
        'subsample': trial.suggest_float('subsample', 0.5, 1.0),
        'colsample_bytree': trial.suggest_float('colsample_bytree', 0.5, 1.0),
        'lambda': trial.suggest_loguniform('lambda', 1e-8, 10.0),
        'alpha': trial.suggest_loguniform('alpha', 1e-8, 10.0),
    }
    dtrain = xgb.DMatrix(X_train, label=y_train)
    dval = xgb.DMatrix(X_val, label=y_val)
    booster = xgb.train(params, dtrain, evals=[(dval,'val')], num_boost_round=1000, early_stopping_rounds=20, verbose_eval=False)
    pred = booster.predict(dval)
    return roc_auc_score(y_val, pred)

# load data and split and run study ...
```

---

## 7) Evaluation & metrics logging (MLflow)

* Log params, metrics, model artifact, preprocessor, and training data hash.
* Example:

```python
mlflow.log_param("n_estimators", 1000)
mlflow.log_metric("val_auc", auc)
mlflow.log_artifact("xgb_model.json")
mlflow.log_artifact("preprocessor.joblib")
```

* Create model card (README) with dataset version, features, known bias, expected input ranges.

---

## 8) Model serialization + model card

* Save XGBoost model as JSON: `model.save_model("model.json")`
* Save preprocessor (`joblib.dump`) and `feature_order.json`.
* Model card (YAML/Markdown) describing:

  * Training data (path & hash)
  * Preprocessing steps
  * Feature list and types
  * Intended use cases, limitations
  * Evaluation metrics & date

---

## 9) Inference / scoring API (FastAPI)

Key points:

* Load preprocessor + model at startup
* Validate incoming requests with Pydantic
* Return probabilities & explainability tokens (SHAP optional)
* Add Prometheus metrics

`src/api/inference_app.py` (abridged)

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib, xgboost as xgb
import numpy as np
import pandas as pd
from prometheus_client import Counter

app = FastAPI()
REQUESTS = Counter('inference_requests_total','Total inference requests')

class Item(BaseModel):
    customer_id: str
    age: int = None
    gender: str = None
    annual_income: float = None

@app.on_event("startup")
def load_artifacts():
    global model, preprocessor, feature_order
    preprocessor = joblib.load("/app/artifacts/preprocessor.joblib")
    model = xgb.Booster()
    model.load_model("/app/artifacts/xgb_model.json")
    feature_order = ["age","annual_income","gender"]

@app.post("/predict")
def predict(item: Item):
    REQUESTS.inc()
    df = pd.DataFrame([item.dict()])
    X = preprocessor.transform(df)
    dmatrix = xgb.DMatrix(X)
    pred = model.predict(dmatrix, ntree_limit=model.best_ntree_limit)
    return {"customer_id": item.customer_id, "proba": float(pred[0])}
```

Dockerfile snippet:

```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY src/ /app/src
EXPOSE 8000
CMD ["uvicorn", "src.api.inference_app:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 10) Batch scoring (notes)

* For large datasets, use Spark (PySpark), Dask, or Polars to preprocess in parallel then call `xgboost` (use `xgb.DMatrix` from NumPy arrays).
* For extremely large scale, do distributed training with XGBoost's Rabit or use `xgboost.dask` for Dask cluster.

---

## 11) CI/CD & testing checklist

* ✅ Unit tests for preprocessing (shapes, types, missing handling)
* ✅ Integration tests (train a tiny dataset to assert pipeline flows)
* ✅ Model performance gate (AUC >= baseline)
* ✅ Lint & formatting (black, flake8)
* ✅ Container build test (smoke inference)
* ✅ Security scan for images
* ✅ Model registry step (MLflow) triggered on successful training
* ✅ Canary release: route small % of traffic to new model -> monitor

Example GitHub Actions pipeline:

* `push` -> run `pytest`, build Docker, run lint
* `workflow_run` -> if tests pass, build image & push to registry
* `deploy` -> push to staging, run canary tests, promote to production

---

## 12) Monitoring & Observability

**Metrics to capture**

* Request count, latency, error rate (Prometheus)
* Model metrics (AUC, PR) on periodic evaluation set (MLflow)
* Data drift metrics: feature distribution KS test / PSI
* Prediction drift: shift in predicted probability distribution
* Input schema violations (use Great Expectations or custom)

**Alerting**

* If PSI > threshold for any feature -> alert
* If model latency > SLA -> alert
* If post-deployment performance drops below baseline -> rollback

**Explainability**

* SHAP explanations stored for a sample of requests (use sampling policy to limit cost)
* Log feature importances periodically

---

## 13) GPU & distributed training

* Use `tree_method='gpu_hist'` and `predictor='gpu_predictor'`.
* For distributed training across nodes, use XGBoost Rabit or use `xgboost.dask`.
* Large datasets: use `external_memory` / `DMatrix` from file to avoid memory blow-up.

---

## 14) Security & privacy

* Encrypt stored artifacts at rest (S3/GCS server-side encryption)
* Use secure credentials (vault/Secrets Manager) for data sources
* PII handling: do not log raw PII in production logs; store hashed IDs
* Use role-based access for model registry

---

## 15) Example production config (configs/default.yaml)

```yaml
data:
  train_csv: data/raw/train.csv
  target_col: target
artifacts_dir: artifacts/
training:
  val_size: 0.2
  num_boost_round: 2000
  early_stopping_rounds: 50
model:
  params:
    booster: gbtree
    objective: binary:logistic
    eval_metric: auc
    eta: 0.05
    max_depth: 6
    subsample: 0.8
    colsample_bytree: 0.8
tracking:
  experiment: xgb_prod_experiment
```

---

## 16) Production-ready tips checklist

* Train reproducibly (set seeds in XGBoost + numpy)
* Log dataset version & hash
* Use `best_iteration` for inference
* Export model + preprocessor + metadata as single bundle
* Implement canary deployment & rollback
* Add continuous monitoring pipeline for drift

---

# PART 2 — XGBoost Interview Coding Problems (15)

Each problem is short, company-interview style. For each: description, expected approach, and solution sketch / code.

---

## Q1 — Simple training & predict

**Problem:** Given `X_train, y_train` (numpy arrays) and `X_test`, train a binary XGBoost classifier with `max_depth=4`, `learning_rate=0.1`, `n_estimators=100` and return predicted probabilities for `X_test`.

**Approach:** Use `xgboost.XGBClassifier` (scikit-learn wrapper).

**Solution:**

```python
from xgboost import XGBClassifier
model = XGBClassifier(max_depth=4, learning_rate=0.1, n_estimators=100, use_label_encoder=False, eval_metric='logloss')
model.fit(X_train, y_train)
proba = model.predict_proba(X_test)[:,1]
```

---

## Q2 — Early stopping with validation set

**Problem:** Implement training with early stopping using a validation split and return best AUC.

**Approach:** Use `fit` with `eval_set` and `early_stopping_rounds` on `XGBClassifier`.

**Solution:**

```python
model.fit(X_train, y_train, eval_set=[(X_val,y_val)], early_stopping_rounds=20, eval_metric='auc', verbose=False)
best_auc = model.best_score  # or compute using sklearn on val preds
```

---

## Q3 — Save and load model (JSON)

**Problem:** Save the trained model to disk and load it back for prediction.

**Approach:** Use `model.get_booster().save_model(path)` or `model.save_model`.

**Solution:**

```python
model.save_model("model.json")
from xgboost import B
m2 = xgb.Booster()
m2.load_model("model.json")
# For sklearn wrapper:
model2 = XGBClassifier()
model2.load_model("model.json")
```

---

## Q4 — Predict with preprocessor

**Problem:** Given a saved `preprocessor.joblib` and `model.json`, write a function `predict_row(row_dict)` that returns probability.

**Solution:**

```python
def predict_row(row_dict):
    import joblib, xgboost as xgb, pandas as pd
    pre = joblib.load("preprocessor.joblib")
    df = pd.DataFrame([row_dict])
    X = pre.transform(df)
    dmatrix = xgb.DMatrix(X)
    booster = xgb.Booster()
    booster.load_model("model.json")
    return float(booster.predict(dmatrix)[0])
```

---

## Q5 — Imbalanced dataset trick

**Problem:** You have extreme imbalance (1% positive). Which XGBoost parameter to use and how to set it?

**Answer:** Use `scale_pos_weight = (neg / pos)` to scale the positive class. Also consider `max_depth` and `subsample` to reduce overfitting, and use proper evaluation metric (AUC/PR).

---

## Q6 — Feature importance extraction

**Problem:** Return top-5 features by gain from trained booster.

**Solution:**

```python
booster = model.get_booster()
importance = booster.get_score(importance_type='gain')
# importance is {f'feature_index': score}
sorted_imp = sorted(importance.items(), key=lambda x: x[1], reverse=True)[:5]
```

If using sklearn wrapper, map indices to column names with preprocessor's feature names.

---

## Q7 — Early detection of data drift (code)

**Problem:** Write a function that computes PSI (population stability index) between train and production arrays for a single feature.

**Solution (sketch):**

```python
import numpy as np

def psi(expected, actual, bins=10):
    cutpoints = np.linspace(0,100,bins+1)
    exp_percents = np.histogram(expected, bins=cutpoints)[0] / len(expected)
    act_percents = np.histogram(actual, bins=cutpoints)[0] / len(actual)
    eps = 1e-6
    psi_val = np.sum((exp_percents - act_percents) * np.log((exp_percents+eps)/(act_percents+eps)))
    return psi_val
```

---

## Q8 — Save model to MLflow and register

**Problem:** Minimal snippet to log model to MLflow.

**Solution:**

```python
import mlflow, mlflow.xgboost
with mlflow.start_run():
    mlflow.log_param("max_depth", 6)
    mlflow.xgboost.log_model(model.get_booster(), artifact_path="model")
```

---

## Q9 — GPU training flag

**Problem:** Provide params for GPU accelerated XGBoost training.

**Answer:**

```python
params = {
    'tree_method': 'gpu_hist',
    'predictor': 'gpu_predictor',
    'gpu_id': 0
}
```

---

## Q10 — Partial dependence (single feature) quick check

**Problem:** How to compute a simple partial dependence estimate for numeric feature `f` (no sklearn PDP tool)?

**Approach:** For several points along `f`'s range, replace column `f` in validation set with fixed value, predict mean probability, plot (value vs mean_pred). Return list.

**Sketch code:**

```python
def pdp(feature, X_valid, model, values):
    preds = []
    for v in values:
        Xc = X_valid.copy()
        Xc[:, feature_index] = v
        preds.append(model.predict(xgb.DMatrix(Xc)).mean())
    return preds
```

---

## Q11 — Ensemble with LightGBM & XGBoost (stacking)

**Problem:** Simple stacking: combine XGBoost & LightGBM predictions with logistic regression meta-learner.

**Sketch approach:**

1. 5-fold cross-validation: generate out-of-fold predictions from each base model.
2. Train logistic regression on OOF preds (meta features).
3. Use base models to predict test set, feed to meta model.

Key: avoid leakage.

---

## Q12 — Save & load feature names mapping after one-hot

**Problem:** After OneHotEncoding, how to map model feature indices back to original names?

**Solution:** Use `ohe.get_feature_names_out(['gender'])` + numeric columns order. Save list as `feature_names.npy`.

---

## Q13 — Explain missing-values behavior

**Problem:** Where does XGBoost send missing values when splitting?

**Answer:** At training, for each split XGBoost learns the default direction (left/right) for missing values by trying both and selecting the direction that yields best gain. During inference, missing values follow that learned default.

---

## Q14 — Implement early stopping manually using xgboost.train

**Problem:** Write `train_with_early_stop` that uses `xgb.train` with early stopping rounds and returns best iteration.

**Sketch solution:**

```python
evals = [(dtrain,'train'), (dval,'val')]
bst = xgb.train(params, dtrain, num_boost_round=2000, evals=evals, early_stopping_rounds=50)
best_iter = bst.best_iteration
```

Note: `bst.best_ntree_limit` also available.

---

## Q15 — Safe inference when model.best_ntree_limit absent

**Problem:** For models saved from different versions, best_ntree_limit may not exist. How to predict safely?

**Answer:** Use:

```python
nt = getattr(model, "best_ntree_limit", None)
if nt:
    preds = model.predict(dmatrix, ntree_limit=nt)
else:
    preds = model.predict(dmatrix)
```

---

## Bonus: Mini interview question — Explain `scale_pos_weight` mathematically

**Answer sketch:** For logistic objective, with positive class very rare, `scale_pos_weight` weighs gradient/hessian for positive class by factor = `neg/pos`, causing algorithm to optimize weighted loss that balances classes.

---
