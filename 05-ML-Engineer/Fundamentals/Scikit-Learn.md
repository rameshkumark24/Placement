# ✅ ONE-PAGE SCIKIT-LEARN CHEAT SHEET  
(Fresher-Friendly + Interview-Ready)

---

## ✅ 1. Core Imports

```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
````

---

## ✅ 2. Train-Test Split

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
```

➡️ Prevents **overfitting & data leakage**

---

## ✅ 3. Data Preprocessing

### 🔹 Scaling

```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

---

### 🔹 Label Encoding (Target Only)

```python
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
y = le.fit_transform(y)
```

---

### 🔹 One Hot Encoding (Categorical Features)

```python
from sklearn.preprocessing import OneHotEncoder
encoder = OneHotEncoder()
X = encoder.fit_transform(X)
```

---

### 🔹 Handling Missing Values

```python
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy="mean")
X = imputer.fit_transform(X)
```

---

## ✅ 4. Supervised Learning Models

### 🔹 Regression

```python
from sklearn.linear_model import LinearRegression
model = LinearRegression()
```

```python
from sklearn.linear_model import LogisticRegression
model = LogisticRegression()
```

---

### 🔹 Classification

```python
from sklearn.tree import DecisionTreeClassifier
model = DecisionTreeClassifier()
```

```python
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier()
```

```python
from sklearn.svm import SVC
model = SVC()
```

```python
from sklearn.neighbors import KNeighborsClassifier
model = KNeighborsClassifier()
```

---

## ✅ 5. Unsupervised Learning

### 🔹 K-Means

```python
from sklearn.cluster import KMeans
model = KMeans(n_clusters=3)
```

---

### 🔹 Hierarchical Clustering

```python
from sklearn.cluster import AgglomerativeClustering
model = AgglomerativeClustering()
```

---

### 🔹 PCA (Dimensionality Reduction)

```python
from sklearn.decomposition import PCA
pca = PCA(n_components=2)
```

---

### 🔹 Apriori (Association Rules – via mlxtend)

```python
from mlxtend.frequent_patterns import apriori
```

---

## ✅ 6. Model Training & Prediction

```python
model.fit(X_train, y_train)
pred = model.predict(X_test)
```

---

## ✅ 7. Model Evaluation

### 🔹 Classification Metrics

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
accuracy_score(y_test, pred)
```

---

### 🔹 Confusion Matrix

```python
from sklearn.metrics import confusion_matrix
confusion_matrix(y_test, pred)
```

---

### 🔹 Regression Metrics

```python
from sklearn.metrics import mean_squared_error, r2_score
mean_squared_error(y_test, pred)
```

---

## ✅ 8. Cross Validation

```python
from sklearn.model_selection import cross_val_score
scores = cross_val_score(model, X, y, cv=5)
```

➡️ Confirms **model stability**

---

## ✅ 9. Hyperparameter Tuning

### 🔹 GridSearchCV

```python
from sklearn.model_selection import GridSearchCV
grid = GridSearchCV(model, param_grid, cv=5)
grid.fit(X_train, y_train)
```

---

### 🔹 RandomizedSearchCV

```python
from sklearn.model_selection import RandomizedSearchCV
search = RandomizedSearchCV(model, param_distributions, cv=5)
```

---

## ✅ 10. Pipelines (🔥 VERY IMPORTANT 🔥)

```python
from sklearn.pipeline import Pipeline

pipeline = Pipeline([
    ("scaler", StandardScaler()),
    ("model", LogisticRegression())
])

pipeline.fit(X_train, y_train)
```

➡️ Prevents **data leakage + clean ML workflow**

---

# ⚡ COMMON INTERVIEW QUESTIONS & ANSWERS

### ✅ Q1. What is Scikit-Learn workflow?

**Load → Split → Preprocess → Train → Predict → Evaluate → Tune**

---

### ✅ Q2. Why Scikit-Learn?

* Easy API
* Fast prototyping
* Huge algorithm support
* Consistent `fit()` / `predict()`
* Production friendly

---

### ✅ Q3. LabelEncoder vs OneHotEncoder?

| LabelEncoder    | OneHotEncoder                 |
| --------------- | ----------------------------- |
| 1 column        | Multiple binary columns       |
| Used for labels | Used for categorical features |

---

### ✅ Q4. What prevents data leakage?

✅ Proper **Train-Test Split**
✅ **Pipeline usage**

---

### ✅ Q5. Difference between fit() & transform()?

* `fit()` → learns parameters
* `transform()` → applies transformation
* `fit_transform()` → both together

---

✅ FINAL TIP FOR INTERVIEW:

> If you know **Pipeline + Cross Validation + GridSearch + Metrics**, you are already at **Company Level**.
