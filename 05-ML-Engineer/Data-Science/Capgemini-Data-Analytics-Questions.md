## 1\. SQL & Database Concepts

### Q1: Write a SQL query to fetch the second highest salary without using MAX().

  * **The "Why":** They want to see if you know "Window Functions" (`DENSE_RANK`), which handle ties (duplicate salaries) better than simple `LIMIT/OFFSET` queries.
  * **Ideal Answer:**
    ```sql
    SELECT salary
    FROM (
        SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
        FROM Employee
    ) t
    WHERE rnk = 2;
    ```
    *Explanation:* "I use `DENSE_RANK()` because if two employees share the highest salary, this function ensures the next distinct salary is correctly ranked as \#2."

### Q2: How would you identify and remove duplicate records?

  * **The "Why":** A classic data cleaning test. They look for safety—identifying first before deleting.
  * **Ideal Answer:**
    "First, I identify them using `GROUP BY` on all columns having a `COUNT > 1`. To safely remove them, I use a **CTE with `ROW_NUMBER()`**:
    ```sql
    WITH CTE AS (
        SELECT *, ROW_NUMBER() OVER (PARTITION BY col1, col2 ORDER BY id) as rn
        FROM Table
    )
    DELETE FROM CTE WHERE rn > 1;
    ```
    This keeps one original instance and deletes the duplicates."

### Q3: Difference between INNER, LEFT, and FULL JOIN with examples.

  * **The "Why":** Basic set theory. Can you explain it to a non-technical person?
  * **Ideal Answer:**
      * **Inner Join:** Returns only matching rows (e.g., Customers *who have placed* Orders).
      * **Left Join:** Returns all rows from the left table and matches from the right (e.g., *All* Customers, even those with *no* Orders yet).
      * **Full Join:** Returns everything from both tables (e.g., All Customers and All Orders, regardless of matches).

[Image of SQL Joins Venn Diagram]

### Q4: Calculate monthly average sales per product.

  * **The "Why":** Tests date manipulation functions (`DATE_TRUNC` or `FORMAT`).
  * **Ideal Answer:**
    ```sql
    SELECT Product_ID,
           DATE_TRUNC('month', Date) as Sale_Month, -- Use FORMAT() in SQL Server
           AVG(Sales_Amount) as Avg_Sales
    FROM Sales
    GROUP BY Product_ID, DATE_TRUNC('month', Date);
    ```

### Q5: OLTP vs. OLAP. Where do you use each?

  * **The "Why":** Do you understand system architecture?
  * **Ideal Answer:**
    "**OLTP (Online Transaction Processing)** is for day-to-day operations (fast inserts/updates), like an ATM or e-commerce checkout.
    **OLAP (Online Analytical Processing)** is for analysis and reporting (fast reads of historical data), like a Data Warehouse used for generating Year-End reports."

-----

## 2\. Python & Data Processing

### Q6: How to handle missing values in 1 million rows (3 techniques)?

  * **The "Why":** Do you understand context? You shouldn't just "delete" everything.
  * **Ideal Answer:**
    1.  **Deletion:** If missing data is \<5% and random, drop the rows.
    2.  **Imputation (Mean/Median):** Fill with Median (if skewed) or Mean (if normal) for numerical columns to preserve data size.
    3.  **Model-Based Imputation:** Use a regression model (like KNN) to predict the missing value based on other columns (most accurate but computationally expensive).

### Q7: Python snippet for top 5 most frequent words.

  * **The "Why":** Tests proficiency with standard libraries.
  * **Ideal Answer:**
    ```python
    from collections import Counter
    import re

    # Combine text, lowercase, remove punctuation
    text = " ".join(df['text_column'].astype(str)).lower()
    words = re.findall(r'\b\w+\b', text)

    # Get top 5
    print(Counter(words).most_common(5))
    ```

### Q8: Pandas merge() vs. join() vs. concat().

  * **The "Why":** Do you know how to combine datasets correctly?
  * **Ideal Answer:**
      * **`merge()`:** Joins on columns (like SQL Join). Best for joining two different datasets on a key (e.g., `ID`).
      * **`join()`:** Joins on the Index by default. Best for quick lookups.
      * **`concat()`:** Stacks dataframes vertically (appending rows) or horizontally. Best for combining monthly files into a yearly dataset.

### Q9: How to normalize skewed data?

  * **The "Why":** Tests statistical knowledge for Machine Learning preparation.
  * **Ideal Answer:**
    "If the data is right-skewed (long tail), I would apply a **Log Transformation** or **Square Root Transformation** to compress the larger values and make the distribution more normal (Gaussian)."

### Q10: PySpark vs. Pandas.

  * **The "Why":** Big Data capability test.
  * **Ideal Answer:**
    "Pandas runs on a single machine (in-memory). If the dataset exceeds the RAM (e.g., 50GB+), Pandas fails. **PySpark** runs on a cluster (distributed computing), so it can process Terabytes of data by splitting the work across multiple nodes."

-----

## 3\. Power BI & Visualization

### Q11: How to create a dashboard for Sales KPIs?

  * **The "Why":** Design thinking.
  * **Ideal Answer:**
    "I focus on the **'Z' layout**:
    1.  **Top:** High-level 'Card' visuals for Headline KPIs (Total Sales, Revenue, Profit).
    2.  **Middle:** Trend lines (Sales over time) and Category breakdowns (Sales by Region).
    3.  **Side/Top:** Slicers for interactivity (Year, Region).
        I ensure colors are consistent and data is readable at a glance."

### Q12: Calculated Column vs. Measure.

  * **The "Why":** Performance optimization test.
  * **Ideal Answer:**
      * **Calculated Column:** Computed row-by-row and stored in RAM. Updates only when data refreshes. (Good for Slicers).
      * **Measure:** Computed on the fly based on user filters (CPU intensive). (Good for numerical aggregations like Sum/Avg).
      * *Rule of thumb:* Always use Measures for math/aggregations to save memory.

### Q13: DAX for YoY Growth.

  * **The "Why":** Syntax proficiency.
  * **Ideal Answer:**
    ```dax
    YoY Growth =
    VAR CurrentSales = [Total Sales]
    VAR LastYearSales = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
    RETURN
    DIVIDE(CurrentSales - LastYearSales, LastYearSales, 0)
    ```

-----

## 4\. Scenario & Behavioral (The "Real World")

### Q14: Stakeholder wants a misleading chart.

  * **The "Why":** Integrity vs. Diplomacy.
  * **Ideal Answer:**
    "I would not bluntly refuse. Instead, I would build their request, but *also* build the 'correct' version (e.g., a Bar chart instead of a 3D Pie chart). I’d present both and say, 'This version highlights the data difference more clearly for the executives.' Usually, when they see the comparison, they choose the clearer one."

### Q15: Steps of ETL and optimizing for large datasets.

  * **The "Why":** Data Engineering fundamentals.
  * **Ideal Answer:**
      * **Steps:** Extract (Source) -\> Transform (Clean/Join) -\> Load (Warehouse).
      * **Optimization:** Use **Incremental Loading** (only process new data, not full history) and **Partitioning** (process data by Year/Month chunks).

### Q16: Setting up a Cloud Pipeline (AWS/Azure).

  * **The "Why":** Modern tech stack familiarity.
  * **Ideal Answer:**
    "On Azure, for example:
    1.  Ingest raw data into **Data Lake Storage (ADLS)**.
    2.  Use **Azure Data Factory** for orchestration.
    3.  Process/clean data using **Databricks (Spark)**.
    4.  Load into **Synapse Analytics** for reporting in Power BI."

### Q17: Batch vs. Real-time Streaming.

  * **The "Why":** Use-case selection.
  * **Ideal Answer:**
    "**Batch** is for periodic updates (e.g., Payroll or Daily Sales Reports) where a delay is acceptable.
    **Streaming** is for instant needs (e.g., Fraud Detection or Stock Trading) where data must be processed the millisecond it arrives."

### Q18: Metrics for Customer Churn.

  * **The "Why":** Business acumen.
  * **Ideal Answer:**
    "I would track **Churn Rate** (percentage leaving), **Customer Lifetime Value (CLV)**, and **Retention Rate**. I’d visualize this by 'Cohort Analysis' to see if newer customers are leaving faster than older ones."

### Q19: Revenue dropped 10%. How to diagnose?

  * **The "Why":** Analytical problem solving.
  * **Ideal Answer:**
    "I would use a **Drill-Down approach**:
    1.  **By Time:** Did it drop suddenly or gradually?
    2.  **By Region:** Is a specific country underperforming?
    3.  **By Product:** Did a top-selling product go out of stock?
    4.  **By Segment:** Did we lose a specific customer type (e.g., Enterprise clients)?
        This isolates the root cause."

### Q20: Insights from unstructured data (Reviews/Emails).

  * **The "Why":** NLP awareness.
  * **Ideal Answer:**
    "I would use **Sentiment Analysis** to classify reviews as Positive/Negative/Neutral. Then, I’d use **Keyword Extraction** (Word Cloud) to see *why* they are negative (e.g., frequent mention of 'Delivery Delay'). I'd present this as a 'Customer Sentiment Scorecard'."
