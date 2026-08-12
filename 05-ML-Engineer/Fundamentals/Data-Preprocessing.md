# ✅ ONE-PAGE DATA PREPROCESSING PIPELINE  
(Fresher + Company-Level Interview & Project Ready)

---

## 🚀 0. Import & Load Data

```python
import pandas as pd
import numpy as np

df = pd.read_csv("data.csv")
````

---

## 🚀 1. Inspect Data (ALWAYS FIRST)

```python
df.head()
df.tail()
df.info()
df.describe()
df.shape
df.dtypes
```

### 🎯 Purpose:

* Understand columns
* Detect missing values
* Identify numerical vs categorical
* Check data types & scale

---

## 🚀 2. Handle Missing Values

### 🔍 Identify

```python
df.isnull().sum()
```

### ✅ Handle in Pandas

```python
df.dropna()                      # Remove rows
df.fillna(0)                     # Constant fill
df.fillna(df.mean())             # Numerical
df.fillna(df.mode().iloc[0])     # Categorical
```

### ✅ ML-Friendly (BEST PRACTICE)

```python
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy="mean")
X = imputer.fit_transform(X)
```

---

## 🚀 3. Handle Categorical Data

### ✅ Label Encoding (Binary / Ordinal)

```python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()
df["gender"] = le.fit_transform(df["gender"])
```

### ✅ One-Hot Encoding (Nominal)

```python
pd.get_dummies(df, drop_first=True)
```

### ✅ ML Pipeline Encoding (BEST)

```python
from sklearn.preprocessing import OneHotEncoder
```

---

## 🚀 4. Handle Outliers

### ✅ Using IQR Method

```python
Q1 = df["age"].quantile(0.25)
Q3 = df["age"].quantile(0.75)
IQR = Q3 - Q1

filtered_df = df[
    (df["age"] >= Q1 - 1.5 * IQR) &
    (df["age"] <= Q3 + 1.5 * IQR)
]
```

---

## 🚀 5. Feature Scaling

### ✅ Standardization (Most Used)

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

### ✅ Normalization (0–1 Range)

```python
from sklearn.preprocessing import MinMaxScaler

scaler = MinMaxScaler()
X_scaled = scaler.fit_transform(X)
```

### ❗ When to Scale?

✅ Logistic Regression
✅ SVM
✅ KNN
✅ Neural Networks

❌ Not needed for:

* Decision Tree
* Random Forest
* XGBoost

---

## 🚀 6. Feature Engineering

### ✅ Create New Features

```python
df["income_yearly"] = df["income_monthly"] * 12

df["age_group"] = df["age"].apply(
    lambda x: "Adult" if x >= 18 else "Child"
)
```

### ✅ Binning

```python
pd.cut(df["age"], bins=[0,18,40,60,100])
```

### ✅ Date Features

```python
df["year"] = pd.to_datetime(df["date"]).dt.year
df["month"] = pd.to_datetime(df["date"]).dt.month
df["weekday"] = pd.to_datetime(df["date"]).dt.weekday
```

---

## 🚀 7. Remove Duplicates

```python
df.drop_duplicates(inplace=True)
```

---

## 🚀 8. Remove Irrelevant Columns

```python
df.drop(["id", "name"], axis=1, inplace=True)
```

---

## 🚀 9. Train-Test Split (MANDATORY)

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
```

---

## 🚀 10. Build Preprocessing Pipeline (🔥 BEST PRACTICE 🔥)

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.impute import SimpleImputer
from sklearn.linear_model import LogisticRegression

pipeline = Pipeline([
    ("imputer", SimpleImputer(strategy="mean")),
    ("scaler", StandardScaler()),
    ("model", LogisticRegression())
])

pipeline.fit(X_train, y_train)
```

✅ Prevents **data leakage**
✅ Ensures **clean ML workflow**

---

## 🚀 11. Feature Selection

### ✅ Correlation

```python
df.corr()
```

### ✅ SelectKBest

```python
from sklearn.feature_selection import SelectKBest, f_classif

selector = SelectKBest(score_func=f_classif, k=10)
X_new = selector.fit_transform(X, y)
```

### ✅ Tree-Based Feature Importance

```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier()
model.fit(X, y)
model.feature_importances_
```

---

## 🚀 12. Final Model Training

```python
model.fit(X_train, y_train)
```

---

## 🚀 13. Model Evaluation

### ✅ Classification

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
```

### ✅ Regression

```python
from sklearn.metrics import mean_squared_error, r2_score
```

---

# 🎯 FULL DATA PREPROCESSING FLOW (REAL-WORLD)

1. Load Data
2. Understand Data
3. Handle Missing Values
4. Handle Categorical Data
5. Detect & Treat Outliers
6. Feature Scaling
7. Feature Engineering
8. Remove Duplicates
9. Remove Irrelevant Columns
10. Train-Test Split
11. Build Pipeline
12. Feature Selection
13. Model Training
14. Evaluation

✅ This is **production-level ML workflow**

# 🚀 TOP 25 DATA PREPROCESSING INTERVIEW QUESTIONS (WITH ANSWERS)

(Company-Level Pack)

---

## 1️⃣ What is data preprocessing?

**Answer:**
Cleaning & transforming raw data into ML-ready format.

Includes:

* Missing values
* Encoding
* Scaling
* Feature engineering
* Feature selection

---

## 2️⃣ Why is data preprocessing important?

**Answer:**
Because raw data contains:

* Missing values
* Noise
* Duplicates
* Outliers

Improves:
✅ Accuracy
✅ Stability
✅ Training speed

---

## 3️⃣ How do you handle missing values?

```python
df.dropna()
df.fillna(0)
df.fillna(df["col"].mean())
```

✅ Best ML method → `SimpleImputer`

---

## 4️⃣ dropna() vs fillna()?

* `dropna()` → removes rows
* `fillna()` → fills values

---

## 5️⃣ How do you detect outliers?

✅ IQR
✅ Z-score
✅ Boxplot
✅ Isolation Forest

---

## 6️⃣ How do you treat outliers?

* Remove
* Cap (winsorize)
* Transform (log/sqrt)
* Use robust models (Random Forest, XGBoost)

---

## 7️⃣ What is feature scaling?

**Answer:**
Transforms features to same scale so no feature dominates.

---

## 8️⃣ StandardScaler vs MinMaxScaler?

| StandardScaler    | MinMaxScaler    |
| ----------------- | --------------- |
| Mean = 0, Std = 1 | Range 0–1       |
| LR, SVM, KNN      | Neural Networks |

---

## 9️⃣ Do tree models need scaling?

❌ No. Trees split on thresholds, not distance.

---

## 🔟 What is categorical encoding?

Converting text → numbers using:

* Label Encoding
* One-Hot Encoding
* Target Encoding (advanced)

---

## 11️⃣ LabelEncoder vs OneHotEncoder?

* LabelEncoder → 0,1,2…
* OneHotEncoder → Binary columns
  ✅ Use OneHot if category is **non-ordinal**

---

## 12️⃣ What is feature engineering?

Creating new useful features.

Examples:

* age → age_group
* date → year/month
* income → yearly income

---

## 13️⃣ What is feature selection?

Choosing most important features to:
✅ Reduce overfitting
✅ Improve accuracy

Methods:

* Correlation
* SelectKBest
* Tree importance

---

## 14️⃣ What is a data pipeline?

Pipeline = preprocessing + model in one flow.
✅ Prevents data leakage
✅ Keeps transformations consistent

---

## 15️⃣ What is data leakage?

Test data influencing training.

Example:
❌ Scaling before train-test split
✅ Fix → Use pipeline

---

## 16️⃣ Why is train-test split needed?

To test model on **unseen data** and avoid overfitting.

---

## 17️⃣ How do you check imbalanced data?

```python
df["target"].value_counts()
```

---

## 18️⃣ How do you handle imbalanced data?

* SMOTE
* Undersampling
* Class weights
* XGBoost / CatBoost

---

## 19️⃣ What is normalization?

Scaling values between **0 and 1**.
Used mainly for **Neural Networks**.

---

## 20️⃣ How to remove duplicates?

```python
df.drop_duplicates(inplace=True)
```

---

# 🔥 SUPER BONUS (ADVANCED COMPANY QUESTIONS)

---

## 21️⃣ High-cardinality encoding methods?

✅ Target Encoding
✅ Hash Encoding
✅ CatBoost Encoder

---

## 22️⃣ Why scaling not needed in Random Forest?

Because trees split by **value comparison**, not distance.

---

## 23️⃣ Normalization vs Standardization?

* Normalization → 0–1 range
* Standardization → Mean 0, Std 1

---

## 24️⃣ When to use MinMaxScaler?

✅ Neural Networks
✅ CNN / RNN
✅ Bounded activation functions

---

## 25️⃣ What is One-Hot Encoding Trap?

Dummy variable trap → **Multicollinearity**
✅ Fix → `drop_first=True`

---
