## 🔹 Project 1: AI-Driven Sales Forecasting System

### 🗣️ HR-Friendly Project Explanation (What to Say)
This project focuses on predicting future sales for retail stores using historical sales data. I worked with a dataset of around **50,000 sales records** and built an **end-to-end forecasting pipeline**.

I transformed raw transaction data into a structured time-series format and engineered features to capture **historical trends** and **seasonality**. Since different stores show different sales behaviors, I grouped stores based on **performance patterns** and trained **separate models** for each group.

This approach improved prediction accuracy compared to a single global model. Finally, I automated future sales predictions and built a **Power BI dashboard** to visualize forecasts and trends for business users.

This project demonstrates my skills in **data preprocessing, feature engineering, modeling, evaluation,** and **business-focused visualization.**

---

### ❓ Expected HR / Technical Cross Questions + Safe Answers

**Q1. Why did you aggregate daily data into monthly data?**  
Daily data contains a lot of noise. Monthly aggregation smooths fluctuations, captures seasonal trends better, and aligns with how businesses typically review sales.

**Q2. Why did you use lag and rolling features?**  
They help the model learn from past sales patterns and recent trends, which are essential for forecasting.

**Q3. Why not random train-test split?**  
In forecasting, future data should not influence past predictions. A time-based split avoids data leakage.

**Q4. Why did you cluster stores before modeling?**  
Stores behave differently. Cluster-wise modeling reduces variance and improves prediction accuracy.

**Q5. Why Random Forest instead of XGBoost or LSTM?**  
Random Forest handles non-linearity well, works reliably with engineered features, and requires less tuning compared to advanced models.

**Q6. How did you measure improvement?**  
I used **MAE** and **RMSE** to evaluate both average error and penalize large forecasting mistakes.

**Q7. How is this useful for business?**  
It helps management plan inventory, staffing, and promotions more effectively.

---

## 🔹 Project 2: Fashion Product Recommendation System

### 🗣️ HR-Friendly Project Explanation (What to Say)
This project is a **computer-vision-based fashion recommendation** system that suggests visually similar products. I worked with around **44,000 fashion images**.

I used a **pre-trained deep learning model** to extract visual features such as color, texture, and style. These features were then compared to identify similar products.

When a user uploads an image, the system returns **visually similar fashion items** through a simple web interface.

This project demonstrates my experience with **deep learning-based feature extraction, similarity search,** and **deploying AI solutions.**

---

### ❓ Expected HR / Technical Cross Questions + Safe Answers

**Q1. Why did you use ResNet50?**  
It is a pre-trained model that extracts high-quality visual features and reduces training time.

**Q2. Why not train your own CNN model?**  
Training from scratch requires a large labeled dataset and high compute. Pre-trained models are more efficient and reliable.

**Q3. How do you compare two images?**  
By comparing the distance between their feature representations.

**Q4. Why Euclidean distance?**  
It is simple, efficient, and works well for similarity-based feature comparison.

**Q5. Did you normalize the features?**  
Yes, normalization ensures fair comparison across images.

**Q6. Is this a recommendation or classification problem?**  
It is a **similarity-based recommendation** problem, not classification.

**Q7. Where can this be used in real life?**  
In **e-commerce platforms** for product discovery and personalized recommendations.

---

## 🔹 Project 3: Food Delivery Time Prediction System

### 🗣️ HR-Friendly Project Explanation (What to Say)
This project predicts **food delivery time** based on multiple real-world factors. I built an end-to-end machine learning application using features such as **distance, weather conditions, traffic density, order type,** and **delivery location**.

I performed **data cleaning, feature engineering,** and experimented with multiple regression models to identify the best-performing one.

The final model achieved **over 90% prediction accuracy**, and I deployed it as a **web application** where users can input order details and receive estimated delivery times.

This project highlights my ability to **work with real-world data, compare ML models,** and **deploy predictive systems.**

---

### ❓ Expected HR / Technical Cross Questions + Safe Answers

**Q1. Why is delivery time prediction important?**  
It improves customer experience, optimizes logistics, and reduces delivery delays.

**Q2. What type of problem is this?**  
It is a **regression** problem.

**Q3. Which models did you try?**  
I experimented with **Random Forest, XGBoost,** and **LightGBM.**

**Q4. How did you choose the final model?**  
Based on **performance metrics** and **consistency across test data.**

**Q5. What features were most important?**  
**Distance, traffic conditions,** and **weather** had the highest impact.

**Q6. How did you evaluate performance?**  
Using regression metrics such as **MAE** and **R²**.

**Q7. How was it deployed?**  
As a **web application using Flask** for model inference.
