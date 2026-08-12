# 🚀 EDA (Exploratory Data Analysis) – Company-Level Interview Q&A Sheet

Pure interview-style questions + crisp answers.  
Use this to speak like an experienced ML / DS engineer 👇

---

## 1️⃣ What is EDA?

**Answer:**
EDA (Exploratory Data Analysis) is the process of **examining, summarizing, and visualizing data** to understand its structure, patterns, relationships, and anomalies **before** applying machine learning.

---

## 2️⃣ Why is EDA important?

**Answer:**
Because EDA:

- Reveals **data quality issues** (missing values, outliers, duplicates)
- Shows **distributions & patterns**
- Helps in **feature selection & feature engineering**
- Guides **correct preprocessing choices**
- Avoids building models on **wrong assumptions**

> “The quality of EDA often decides the quality of the final model.”

---

## 3️⃣ What are your typical EDA steps on a new dataset?

**Answer:**

1. Load data, check `shape`, `dtypes`
2. View `head()`, `info()`, `describe()`
3. Analyze **missing values**
4. Study **distributions** of numerical features
5. Analyze **categorical feature frequencies**
6. Detect **outliers**
7. Check **correlations & relationships** with target
8. Visualize using **histograms, boxplots, scatter plots, heatmaps**
9. Identify **feature engineering opportunities**

---

## 4️⃣ How do you detect outliers during EDA?

**Answer:**

- **Box plot**
- **IQR method**  
- **Z-score**
- **Scatter plots** for bivariate outliers
- For advanced cases: **Isolation Forest / Local Outlier Factor**

---

## 5️⃣ How do you handle outliers?

**Answer:**

- **Remove** extreme rows (if clearly incorrect)
- **Cap/floor** values (winsorization)
- **Transform** (log/sqrt) skewed features
- Use **robust models** like tree-based algorithms that are less sensitive

Decision depends on:
- Business context
- Model type
- Impact on performance

---

## 6️⃣ How do you check for missing values?

**Answer:**

```python
df.isnull().sum()
df.isna().mean()
````

Also visualize with:

* Missingness heatmap (e.g., `sns.heatmap(df.isnull())`)

---

## 7️⃣ How do you decide how to treat missing values?

**Answer:**

Based on:

* **Type of feature** (numerical / categorical)
* **Percentage missing**
* **Importance of feature**

Common strategies:

* Drop rows/columns (if too many missing)
* Fill with **mean/median** (numerical)
* Fill with **mode** (categorical)
* Use advanced imputers (KNN, MICE, domain rules)

---

## 8️⃣ What is univariate, bivariate, and multivariate EDA?

**Answer:**

* **Univariate** → 1 feature at a time

  * Example: Histogram of `age`
* **Bivariate** → 2 features

  * Example: Scatter `age` vs `salary` or `boxplot(salary by gender)`
* **Multivariate** → 3+ features

  * Example: Pairplots, heatmaps, multivariate models

---

## 9️⃣ How do you perform EDA for numerical features?

**Answer:**

* Summary stats: `mean`, `median`, `std`, `min`, `max`
* Visuals: **histogram, KDE plot, box plot**
* Check skewness, outliers, and relationship with target (scatter/box)

---

## 🔟 How do you perform EDA for categorical features?

**Answer:**

* `value_counts()` for frequency
* Bar plots
* Cross-tab with target variable
* Analyze rare categories
* Check for **high-cardinality** features

---

## 1️⃣1️⃣ What is correlation, and why is it important in EDA?

**Answer:**
Correlation measures the **strength and direction of linear relationship** between two numerical variables.

Used for:

* **Feature selection**
* Detecting **multicollinearity**
* Understanding how **features relate to target**

---

## 1️⃣2️⃣ What is multicollinearity and how do you check it in EDA?

**Answer:**

Multicollinearity = When two or more features are **highly correlated** with each other.

Detection:

* Correlation matrix / heatmap
* **VIF (Variance Inflation Factor)**

If high:

* Drop one of the correlated features
* Combine them
* Use regularization models

---

## 1️⃣3️⃣ What kind of visualizations do you commonly use in EDA?

**Answer:**

* **Histogram / KDE** → Distribution
* **Box plot** → Outliers
* **Scatter plot** → Relationships
* **Bar chart** → Category comparison
* **Heatmap** → Correlation
* **Pairplot** → Multi-feature patterns
* **Line plot** → Time series

---

## 1️⃣4️⃣ What is the difference between EDA and data preprocessing?

**Answer:**

* **EDA** → Understanding and analyzing data
* **Preprocessing** → Cleaning and transforming data

EDA answers **“What is happening in the data?”**
Preprocessing answers **“How do I prepare this for a model?”**

---

## 1️⃣5️⃣ How do you approach EDA for a classification problem?

**Answer:**

* Analyze **class balance** (target distribution)
* Study **numerical features** by target class (boxplots, violin plots)
* Study **categorical features** vs target (stacked bar charts)
* Check **correlation** of features with target (point-biserial, etc.)
* Look for **separability** between classes in key features

---

## 1️⃣6️⃣ How do you approach EDA for a regression problem?

**Answer:**

* Analyze **target variable distribution** and skewness
* Check **linear/non-linear relationships** between features and target (scatter plots)
* Correlation with target
* Residual Explorations (later in modeling step)

---

## 1️⃣7️⃣ How do you handle highly imbalanced target during EDA?

**Answer:**

* Check **class distribution** (value_counts or pie chart)
* Use **stratified train-test split**
* Consider:

  * Oversampling (SMOTE)
  * Undersampling
  * Class weights
* Use appropriate metrics later → **F1, ROC-AUC, PR-AUC**, not just accuracy

---

## 1️⃣8️⃣ What is the role of domain knowledge in EDA?

**Answer:**

Domain knowledge helps to:

* Interpret patterns correctly
* Identify **unrealistic values**
* Create **meaningful features**
* Decide what is an **outlier** vs a valid extreme case
* Make **business-relevant insights**

---

## 1️⃣9️⃣ How do you perform EDA on very large datasets (millions of rows)?

**Answer:**

* Use **sampling** (e.g., `df.sample(100000)`)
* Use **aggregations** instead of raw rows
* Work with **chunked reading** (`chunksize` in pandas)
* Push EDA logic closer to database/SQL when possible

---

## 2️⃣0️⃣ What is your one-line answer to: “How do you perform EDA in a project?” (Use this in interviews)

**Answer:**

> “I start by understanding the data structure and quality using summary statistics and visualizations, then analyze missing values, outliers, distributions, relationships, and feature-target interactions. Based on this, I decide the right preprocessing steps, feature engineering, and model strategy.”

---

# 🎯 BONUS: 5 Advanced EDA Questions (For Good Companies)

---

## 2️⃣1️⃣ How do you handle high-cardinality categorical variables during EDA?

**Answer:**

* Check **top-k categories**
* Group rare categories as “Other”
* Use **target mean plots** (avg target per category)
* Plan encodings like **Target Encoding / Hashing** later

---

## 2️⃣2️⃣ How do you check if a feature is useful or not?

**Answer:**

* Correlation / relationship with target
* Low variance → often not useful
* High missingness → may not be worth imputing
* Use simple models (like decision tree/logistic) for quick feature importance

---

## 2️⃣3️⃣ How do you identify skewness and what do you do about it?

**Answer:**

* Check histogram / KDE
* use `df.skew()`
* If highly skewed:

  * Apply log/Box-Cox transform
  * Or binning for certain features

---

## 2️⃣4️⃣ How do you integrate EDA into a full ML workflow?

**Answer:**

1. EDA → Understand data & issues
2. Decide **preprocessing strategy**
3. Perform **feature engineering** based on patterns
4. Select appropriate **models & metrics**
5. Use EDA insights to **interpret model behavior**

---

## 2️⃣5️⃣ What mistakes should be avoided during EDA?

**Answer:**

* Looking at **test data** during EDA
* Making decisions based on **very small samples**
* Overfitting feature engineering based on noise
* Ignoring **data leakage** (using future info)

---

# ✅ FINAL NOTE

If you can confidently explain:

* EDA steps
* Outliers, missing values, distributions
* Correlation, multicollinearity
* Visualizations & how you interpret them

➡ You will **clear EDA section in any DS/ML interview**.
