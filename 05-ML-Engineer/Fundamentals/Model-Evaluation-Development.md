
# MODEL DEVELOPMENT & MODEL EVALUATION – FULL NOTES

## PART 1 — MODEL DEVELOPMENT (Full Pipeline)

Model development means the entire workflow from data loading → to training → to deployment.
Here is the correct real-world + academic structure.

### 1. Problem Definition

Understand:
- **Type of ML task**
  - Classification (spam/ham, fraud detection)
  - Regression (house price prediction)
  - Clustering
  - NLP, CV
- **Target variable (y)**
- **Business goals**
- **Evaluation metrics required**

### 2. Data Collection

Methods:
- Through CSV files
- Databases
- APIs
- Web scraping
- Sensors, logs
- Cloud storage

Store data with:
- Pandas
- SQL
- Data lakes (for big companies)

### 3. Data Understanding (EDA)

This step finds:
- Structure
- Missing values
- Outliers
- Feature types
- Class imbalance
- Distribution patterns

Tools:
- Pandas
- Matplotlib
- Seaborn

### 4. Data Preprocessing

Preprocessing depends on your data.

**4.1 Handling Missing Values**
- Mean/median (numerical)
- Mode (categorical)
- Forward/backward fill
- Drop row/column

**4.2 Outlier Treatment**
- IQR method
- Z-score
- Domain-based rules

**4.3 Categorical Encoding**
- Label Encoding
- One-hot Encoding
- Target Encoding (advanced)

**4.4 Feature Scaling**

Algorithms that need scaling:
- KNN
- SVM
- Logistic Regression
- Neural Networks
- PCA

Techniques:
- StandardScaler
- MinMaxScaler
- RobustScaler

**4.5 Train-Test Split**

train_test_split(X, y, test_size=0.2, random_state=42)


### 5. Feature Engineering

Improve model by creating meaningful features.

Examples:
- Polynomial features
- Interaction features
- Domain-based features
- Date-time features
- Text vectorization (TF-IDF)
- Image preprocessing

### 6. Feature Selection

Remove unwanted or weak features.

Methods:
- **Filter Methods:**
  - Correlation
  - ANOVA
  - Chi-square
- **Wrapper Methods:**
  - RFE
  - Forward/backward selection
- **Embedded Methods:**
  - Lasso
  - Tree-based feature importance

### 7. Model Selection

Choose algorithms based on task:

**Classification:**
- Logistic Regression
- Decision Tree
- Random Forest
- XGBoost
- SVM
- Naive Bayes

**Regression:**
- Linear Regression
- Ridge/Lasso
- Random Forest Regressor
- XGBoost Regressor

**Clustering:**
- K-means
- Agglomerative clustering
- DBSCAN

**Deep Learning:**
- CNN
- RNN/LSTM
- Transformers

### 8. Model Training

Train using:

model.fit(X_train, y_train)


Store:
- Training time
- GPU/CPU usage
- Memory usage

### 9. Hyperparameter Tuning

Improve performance by tuning model parameters.

Methods:
- Grid Search
- Random Search
- Bayesian Optimization
- Optuna
- Hyperband
- AutoML

Example:

GridSearchCV(model, param_grid, cv=5)


### 10. Final Model Build

Use best model + best parameters:

best_model = grid.best_estimator_


---

## PART 2 — MODEL EVALUATION (Detailed)

Model evaluation tells you how good your model is.
Different metrics for:
- Classification
- Regression
- Clustering
- Deep learning

### 1. Classification Evaluation Metrics
(For spam/ham, fraud detection, cancer prediction)

**Confusion Matrix**

TP | FP
FN | TN


**Accuracy**

(TP + TN) / Total


**Precision**
How many predicted positives are correct?

TP / (TP + FP)


**Recall (Sensitivity)**
How many actual positives were detected?

TP / (TP + FN)


**F1-score**
Harmonic mean of precision and recall.

2 * (Precision * Recall) / (Precision + Recall)


**AUC-ROC**
Measures model separation capability.

**When to use what?**

| Situation              | Best metric      |
|------------------------|------------------|
| Balanced data          | Accuracy         |
| Imbalanced data        | F1-score / AUC   |
| When FP is costly      | Precision        |
| When FN is costly      | Recall           |

### 2. Regression Evaluation Metrics
(For price prediction, demand forecasting)

**MAE (Mean Absolute Error)**
Average absolute error: `|y - ŷ|`

**MSE (Mean Square Error)**
Squared error — punishes large errors.

**RMSE**
Square root of MSE.

**R² Score**
How well the model explains variance: `1 – (SSR / SST)`

### 3. Clustering Evaluation Metrics

**Silhouette Score**
Measures separation between clusters (0–1).

**Dunn Index**
Cluster compactness.

**Davies–Bouldin Index**
Lower is better.

**Elbow Method**
Choosing number of clusters.

### 4. Deep Learning Evaluation

- Training loss
- Validation loss
- Validation accuracy
- Overfitting detection
- Confusion matrix

### 5. Cross-Validation

Evaluates model stability.

Types:
- k-fold CV
- Stratified k-fold
- Leave-one-out

Example:

cross_val_score(model, X, y, cv=5)


### 6. Model Monitoring (MLOps)

After deployment:
- Drift detection
- Performance decay
- Logging
- Retraining triggers

Metrics monitored:
- Prediction latency
- Throughput
- Error rate
- Real-world accuracy

---

## FINAL SUMMARY (Interview Ready)

**Model Development Steps**

EDA → Preprocessing → Feature Engineering → Model Selection → Training → Tuning → Evaluation → Deployment

**Model Evaluation Metrics**
- **Classification** → Accuracy, Precision, Recall, F1, ROC-AUC
- **Regression** → MAE, MSE, RMSE, R²
- **Clustering** → Silhouette, DBI, Elbow
