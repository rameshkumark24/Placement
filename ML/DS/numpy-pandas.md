# ✅ ONE-PAGE NUMPY CHEAT SHEET  
(Fresher + Company-Level Interview Ready)

---

## ✅ 1. Basic Import

```python
import numpy as np
````

---

## ✅ 2. Creating Arrays

```python
np.array([1, 2, 3])
np.zeros((3, 3))
np.ones((2, 2))
np.arange(0, 10, 2)
np.linspace(0, 1, 5)
np.eye(3)          # Identity Matrix
```

---

## ✅ 3. Array Operations

```python
a + b
a - b
a * b
a / b

np.dot(a, b)       # Matrix Multiplication
np.sqrt(a)
np.mean(a)
np.sum(a)
np.std(a)
```

---

## ✅ 4. Indexing & Slicing

```python
a[0]
a[-1]
a[1:4]

a[:, 0]           # First column
a[0, :]           # First row
```

---

## ✅ 5. Reshaping & Transpose

```python
a.reshape(3, 4)
a.flatten()
a.T               # Transpose
```

---

## ✅ 6. Stacking Arrays

```python
np.hstack([a, b])
np.vstack([a, b])
```

---

## ✅ 7. Useful NumPy Functions

```python
np.unique(a)
np.argmax(a)
np.argmin(a)
np.where(a > 10)
```

---

## ✅ 8. Random Numbers (Very Important for ML)

```python
np.random.rand(3,3)
np.random.randn(3,3)
np.random.randint(0, 100, size=10)
```

---

## ✅ 9. Broadcasting (Interview Favorite)

```python
a = np.array([1, 2, 3])
a + 10
```

➡️ Automatically adds `10` to all elements

---

## ✅ 10. Boolean Masking

```python
a[a > 5]
```

---

# ⚡ NUMPY INTERVIEW KEY POINTS

✅ Faster than Python lists
✅ Uses C backend → High performance
✅ Supports vectorization
✅ Foundation for Pandas, ML, Deep Learning

---

---

# ✅ ONE-PAGE PANDAS CHEAT SHEET

(Fresher + Company-Level Interview Ready)

---

## ✅ 1. Basic Import

```python
import pandas as pd
```

---

## ✅ 2. Read & Write Data

```python
df = pd.read_csv("data.csv")
df.to_csv("output.csv", index=False)
```

---

## ✅ 3. Understanding the Data

```python
df.head()
df.tail()
df.info()
df.describe()
df.columns
df.shape
```

---

## ✅ 4. Selecting Columns & Rows

```python
df["col"]
df[["col1", "col2"]]

df.iloc[0]          # By index
df.loc[3]           # By label
df.loc[:, "col"]    # Full column
```

---

## ✅ 5. Filtering Data

```python
df[df["age"] > 30]

df[(df["age"] > 30) & (df["salary"] > 50000)]

df[df["gender"] == "Male"]
```

---

## ✅ 6. Handling Missing Values

```python
df.isnull().sum()
df.dropna()
df.fillna(0)
df.fillna(df["age"].mean())
```

---

## ✅ 7. Add & Remove Columns

```python
df["new_col"] = df["salary"] * 12
df.drop("col", axis=1, inplace=True)
```

---

## ✅ 8. GroupBy (Most Important Topic)

```python
df.groupby("department")["salary"].mean()

df.groupby("gender").agg({
    "age": "mean",
    "salary": "sum"
})
```

---

## ✅ 9. Sorting Data

```python
df.sort_values("age")

df.sort_values(["age", "salary"], ascending=[True, False])
```

---

## ✅ 10. Merge & Concat

```python
pd.merge(df1, df2, on="id")

pd.concat([df1, df2])
```

---

## ✅ 11. Apply & Lambda

```python
df["age_group"] = df["age"].apply(
    lambda x: "Adult" if x >= 18 else "Child"
)
```

---

## ✅ 12. Correlation

```python
df.corr()
```

---

## ✅ 13. Value Counts & Unique (Interview Favorite)

```python
df["gender"].value_counts()
df["department"].unique()
df["department"].nunique()
```

---

## ✅ 14. Data Type Conversion

```python
df["age"] = df["age"].astype(int)
```

---

## ✅ 15. Pivot Table

```python
df.pivot_table(
    values="salary",
    index="department",
    aggfunc="mean"
)
```

---

## ✅ 16. Binning Data

```python
pd.cut(df["age"], bins=3)
pd.qcut(df["salary"], q=4)
```
---

TOP 15 REAL PANDAS + NUMPY INTERVIEW QUESTIONS (WITH ANSWERS)
(Company-Level Fresher Standard)

---

## 1️⃣ Difference between NumPy and Pandas?

**Answer:**

- **NumPy** → Numerical computations (arrays, matrices, linear algebra)
- **Pandas** → Data manipulation (tables, missing values, grouping, filtering)

👉 Simply:
- NumPy = **Numbers**
- Pandas = **Tables**

---

## 2️⃣ Difference between Series and DataFrame?

**Answer:**

- **Series** → 1D (single column)
- **DataFrame** → 2D (rows + columns)

👉 Excel Sheet = DataFrame  
👉 One Column = Series  

---

## 3️⃣ loc vs iloc?

**Answer:**

- `loc` → Label based  
- `iloc` → Index/Position based  

```python
df.loc[3, "age"]    # row label 3
df.iloc[3, 1]       # 3rd row, 1st column
````

---

## 4️⃣ How do you handle missing values in Pandas?

```python
df.isnull().sum()        # Check
df.dropna()              # Remove rows
df.fillna(0)             # Fill with constant
df.fillna(df.mean())     # Fill with mean
```

✅ In ML pipelines → `SimpleImputer`

---

## 5️⃣ How to filter rows in Pandas?

```python
df[df["age"] > 30]
df[(df.age > 30) & (df.salary > 50000)]
```

---

## 6️⃣ How to merge two DataFrames?

```python
pd.merge(df1, df2, on="id")
pd.concat([df1, df2])
```

* **Merge** = SQL Join
* **Concat** = Stack rows/columns

---

## 7️⃣ What is Vectorization in NumPy? Why faster?

**Answer:**
Vectorization means operating on entire arrays **without Python loops**.

```python
a + b   # Vectorized
```

✅ Faster because:

* No Python loops
* C-level execution
* SIMD optimizations

---

## 8️⃣ What is Broadcasting in NumPy?

**Answer:**
Allows operations on different shaped arrays.

```python
a = np.array([1,2,3])
b = 5
a + b    # [6, 7, 8]
```

---

## 9️⃣ How to calculate correlation?

```python
df.corr()
```

✅ Used in:

* Feature selection
* Multicollinearity detection
* EDA

---

## 🔟 How to apply custom function to a column?

```python
df["new"] = df["age"].apply(
    lambda x: "Adult" if x >= 18 else "Child"
)
```

✅ Used in Feature Engineering

---

# 🔥 BONUS 5 HIGH-VALUE COMPANY QUESTIONS

---

## 11️⃣ merge() vs join() vs concat()?

| Function | Purpose            |
| -------- | ------------------ |
| merge()  | SQL-style joins    |
| join()   | Index-based join   |
| concat() | Stack rows/columns |

---

## 12️⃣ unique() vs nunique()?

* `unique()` → Returns unique values
* `nunique()` → Returns **count** of unique values

---

## 13️⃣ How to remove duplicates?

```python
df.drop_duplicates()
```

---

## 14️⃣ How to convert data type?

```python
df["age"] = df["age"].astype(int)
```

---

## 15️⃣ Explain groupby() with example

```python
df.groupby("dept")["salary"].mean()
```

✅ GroupBy = **Split → Apply → Combine**
✅ Core concept in Data Analytics

---

# ✅ FINAL CONFIDENCE BOOST

✅ If you know these 15 →
You can clear **Pandas + NumPy fresher interviews confidently**
✅ Used directly in:

* EDA
* Feature Engineering
* Machine Learning Pipelines

---

# ✅ FINAL INTERVIEW TIPS

✅ NumPy → Speed + Math backend
✅ Pandas → Data Cleaning + Analysis
✅ GroupBy + Merge + Apply = 80% Interview Cleared
✅ NumPy + Pandas mastery = Strong ML Foundation
