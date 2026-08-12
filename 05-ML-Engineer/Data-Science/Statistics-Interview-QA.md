# 🚀 STATISTICS – COMPANY-LEVEL INTERVIEW Q&A (TOP 30)
For Data Scientist | ML Engineer | Analyst | AI Engineer

Only crisp, confident, real interview-style answers.

---

# ⭐ BASIC STATISTICS

---

## 1️⃣ Difference between population and sample?

**Answer:**
- **Population** → Entire group
- **Sample** → Subset taken from population  

✅ ML models always learn from **samples**, not the full population.

---

## 2️⃣ What are mean, median, and mode?

**Answer:**
- **Mean** → Average
- **Median** → Middle value
- **Mode** → Most frequent value  

✅ Median is preferred when **outliers exist**.

---

## 3️⃣ What is variance and standard deviation?

**Answer:**
- **Variance** → Spread of data
- **Standard Deviation** → Square root of variance  
✅ Higher STD = More variability.

---

## 4️⃣ What is skewness?

**Answer:**
Skewness shows **asymmetry of data**:
- Left-skewed  
- Right-skewed  
- Zero skew (normal)

✅ Used to decide transformations.

---

## 5️⃣ What is kurtosis?

**Answer:**
Kurtosis measures **peakedness and tail heaviness**.  
High kurtosis = More **extreme outliers**.

---

# ⭐ PROBABILITY & DISTRIBUTIONS

---

## 6️⃣ What is probability?

**Answer:**
Probability measures chance of an event.  
Range → **0 to 1**

---

## 7️⃣ Types of probability?

**Answer:**
- Classical
- Empirical
- Subjective
- Conditional

---

## 8️⃣ What is conditional probability?

**Answer:**
Probability of A given B has occurred.

Formula:
> P(A|B) = P(A ∩ B) / P(B)

---

## 9️⃣ What is Bayes’ Theorem?

**Answer:**
It updates probability using **prior + likelihood + evidence**.  
✅ Used in **Naive Bayes Classifier**.

---

## 🔟 What is a probability distribution?

**Answer:**
It describes how probabilities are spread across values.

Examples:
- Normal
- Binomial
- Poisson
- Exponential

---

## 1️⃣1️⃣ What is normal distribution?

**Answer:**
- Bell-shaped
- Mean = Median = Mode
- Many natural datasets follow it  
✅ Used in scaling & hypothesis testing.

---

## 1️⃣2️⃣ What is the Central Limit Theorem (CLT)?

**Answer:**
> The distribution of sample means becomes normal as sample size increases, even if original data is not normal.

✅ Foundation for inferential statistics.

---

# ⭐ CORRELATION & RELATIONSHIPS

---

## 1️⃣3️⃣ What is correlation?

**Answer:**
Measures strength of relationship between variables.  
Range → **-1 to +1**

---

## 1️⃣4️⃣ Correlation vs causation?

**Answer:**
- **Correlation** → Variables move together  
- **Causation** → One variable causes the other  

✅ ML detects correlation, not causation.

---

## 1️⃣5️⃣ What is covariance?

**Answer:**
It measures **direction of joint variability**, but not normalized like correlation.

---

# ⭐ HYPOTHESIS TESTING

---

## 1️⃣6️⃣ What is hypothesis testing?

**Answer:**
It checks whether an observed result is **statistically significant** or due to chance.

---

## 1️⃣7️⃣ What are null and alternative hypotheses?

**Answer:**
- **H₀ (Null)** → No effect  
- **H₁ (Alternative)** → Effect exists  

---

## 1️⃣8️⃣ What is p-value?

**Answer:**
Probability that the observed result occurred by chance.  
✅ If **p < 0.05 → Reject H₀**

---

## 1️⃣9️⃣ What is a t-test?

**Answer:**
Used to compare **means of two groups**.  
Example: Male vs Female salary.

---

## 2️⃣0️⃣ What is a chi-square test?

**Answer:**
Tests association between **categorical variables**.  
Example: Gender vs Product Choice.

---

# ⭐ SAMPLING

---

## 2️⃣1️⃣ What is sampling?

**Answer:**
Selecting a subset from a population to estimate population behavior.

---

## 2️⃣2️⃣ Types of sampling?

**Answer:**
- Random
- Stratified
- Cluster
- Systematic
- Convenience

---

# ⭐ ML-RELATED STATISTICS

---

## 2️⃣3️⃣ What is bias and variance?

**Answer:**
- **Bias** → Error due to wrong assumptions  
- **Variance** → Error due to over-complex model  
✅ Goal → Balance both (Bias–Variance Tradeoff)

---

## 2️⃣4️⃣ What is overfitting?

**Answer:**
Model performs very well on training data but poorly on unseen test data.

---

## 2️⃣5️⃣ What is underfitting?

**Answer:**
Model is too simple and performs poorly on both train and test sets.

---

## 2️⃣6️⃣ What is multicollinearity?

**Answer:**
When independent variables are highly correlated.

✅ Fix:
- Remove features  
- PCA  
- Regularization  

---

## 2️⃣7️⃣ What is ANOVA?

**Answer:**
Analysis of Variance → Compares **3 or more group means**.

---

## 2️⃣8️⃣ What is a confidence interval?

**Answer:**
A range that likely contains the true population value.
Example:
> “95% CI for mean age: 28–33”

---

# ⭐ DISTRIBUTIONS & REAL ML USE

---

## 2️⃣9️⃣ Binomial vs Poisson distribution?

**Answer:**

- **Binomial** → Fixed trials, success/failure  
- **Poisson** → Events in a fixed time/space interval  

Used in:
- Click prediction
- Call arrival rate
- Traffic modeling

---

## 3️⃣0️⃣ Why is normal distribution important in ML?

**Answer:**
Normal distribution is critical for:
- Z-score scaling
- CLT
- Linear regression assumptions
- Hypothesis testing
- Many parametric ML models

---

# 🎯 PERFECT 1-LINE INTERVIEW ANSWER

If interviewer asks:
**“What role does statistics play in machine learning?”**

✅ Say this:

> “Statistics forms the backbone of machine learning by helping in understanding data distributions, relationships, uncertainty, hypothesis testing, feature selection, and model evaluation. Strong statistics ensures model accuracy, reliability, and interpretability.”

🔥 This answer = instant positive impression.

---
