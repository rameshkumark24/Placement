# 🚀 TABLEAU – COMPLETE COMPANY-LEVEL EXPERT NOTES
(For Data Analyst | BI Developer | Data Scientist | Business Intelligence Roles)

This is a **REAL INDUSTRY WORKFLOW + TOOL KNOWLEDGE + INTERVIEW-SAFE PACK**.

---

# ✅ 1. WHAT IS TABLEAU?

Tableau is a **Business Intelligence & Data Visualization tool** used to:

- Analyze large datasets
- Create interactive dashboards
- Perform Visual Analytics
- Share business insights
- Support data-driven decisions

✅ Famous for **drag-and-drop visualization + fast performance**.

---

# ✅ 2. TABLEAU MAIN COMPONENTS (VERY IMPORTANT)

| Component | Purpose |
|----------|---------|
| Tableau Desktop | Report & Dashboard creation |
| Tableau Server | On-premise sharing |
| Tableau Online | Cloud BI platform |
| Tableau Prep | Data cleaning & shaping |
| Tableau Public | Free public portfolio sharing |
| Tableau Mobile | Mobile dashboard viewing |

---

# ✅ 3. HOW TO ACCESS & INSTALL TABLEAU

1. Download Tableau Desktop:
   → https://www.tableau.com

2. Versions:
   - Trial (14 days)
   - Student License (Free)
   - Professional License (Paid)

3. Tableau Public:
   → Free account for portfolio hosting

---

# ✅ 4. TABLEAU INTERFACE – MAIN AREAS

- **Data Pane** → Dimensions & Measures
- **Shelves** → Rows, Columns
- **Marks Card** → Color, Size, Label, Shape
- **Filters Shelf** → Data filtering
- **Show Me** → Visualization suggestions

---

# ✅ 5. DATA SOURCES SUPPORT (CONNECTIONS)

Tableau connects to:

✅ Excel  
✅ CSV  
✅ SQL Server  
✅ MySQL / PostgreSQL  
✅ Oracle  
✅ Snowflake  
✅ BigQuery  
✅ AWS Redshift  
✅ Web Data Connector  
✅ Google Sheets  
✅ APIs  

Connection Modes:
- **Live Connection**
- **Extract (Hyper)**

---

# ✅ 6. TABLEAU PREP – DATA CLEANING (ETL)

Used for:
- Rename columns
- Remove nulls
- Split / merge columns
- Change datatypes
- Remove duplicates
- Join & Union datasets
- Pivot / Unpivot

✅ Output saved as **.hyper extract**.

---

# ✅ 7. DATA TYPES IN TABLEAU

| Type | Examples |
|------|----------|
| String | Name, City |
| Number | Sales, Quantity |
| Date | Order Date |
| Boolean | Yes/No |
| Geographic | Country, State |

---

# ✅ 8. DIMENSIONS VS MEASURES (INTERVIEW FAVORITE)

| Dimensions | Measures |
|------------|----------|
| Categorical | Numerical |
| Discrete | Continuous |
| Used for grouping | Used for aggregation |

Example:
- City → Dimension
- Sales → Measure

---

# ✅ 9. JOINS & UNIONS IN TABLEAU

### ✅ Joins (Horizontal Combine)
- Inner
- Left
- Right
- Full

### ✅ Union (Vertical Combine)
Stack similar tables row-wise.

---

# ✅ 10. RELATIONSHIPS (NEW MODELING STYLE)

Tableau supports:
- Logical Relationships
- Physical Joins

✅ Helps build **star schema-style models**.

---

# ✅ 11. FILTERS IN TABLEAU

| Filter Type | Use |
|-------------|-----|
| Dimension Filter | Text categories |
| Measure Filter | Numeric ranges |
| Date Filter | Time filtering |
| Context Filter | Performance optimization |
| Extract Filter | Data reduction |

---

# ✅ 12. CALCULATED FIELDS (TABLEAU DAX)

Used for:
- Business logic
- KPIs
- New features

### Example:
```text
Profit = [Sales] - [Cost]
````

```text
IF [Sales] > 50000 THEN "High" ELSE "Low" END
```

---

# ✅ 13. LEVEL OF DETAIL (LOD) EXPRESSIONS (VERY IMPORTANT)

Used to control aggregation level.

| Type    | Purpose              |
| ------- | -------------------- |
| FIXED   | Ignore view level    |
| INCLUDE | Add more granularity |
| EXCLUDE | Remove detail        |

Example:

```text
{ FIXED [Region] : SUM([Sales]) }
```

---

# ✅ 14. TABLEAU VISUALIZATIONS

✅ Bar Chart
✅ Line Chart
✅ Area Chart
✅ Pie / Donut
✅ Tree Map
✅ Heat Map
✅ Scatter Plot
✅ Box Plot
✅ KPI Cards
✅ Map Visualizations
✅ Gantt Chart

---

# ✅ 15. MARKS CARD FEATURES

* Color
* Size
* Label
* Shape
* Tooltip
* Detail

✅ Used to enhance visual meaning.

---

# ✅ 16. DASHBOARDS IN TABLEAU

Used to:

* Combine multiple sheets
* Create business story
* Interactivity between charts

Features:

* Horizontal & vertical containers
* Floating charts
* Dashboard actions

---

# ✅ 17. DASHBOARD ACTIONS (ADVANCED)

| Action           | Use                       |
| ---------------- | ------------------------- |
| Filter Action    | One chart filters another |
| Highlight Action | Highlight selections      |
| URL Action       | Open external links       |
| Parameter Action | Change logic dynamically  |

---

# ✅ 18. PARAMETERS (DYNAMIC CONTROL)

Used to:

* Create What-If analysis
* User input driven logic
* Scenario modeling

---

# ✅ 19. SETS IN TABLEAU

Used to:

* Group important records
* Create dynamic top-N analysis
* Advanced filtering logic

---

# ✅ 20. MAPS & GEO ANALYTICS

Supports:
✅ Country
✅ State
✅ City
✅ Zip Code

Map Types:

* Symbol Map
* Filled Map
* Density Map

---

# ✅ 21. PERFORMANCE OPTIMIZATION IN TABLEAU

✅ Use extracts instead of live
✅ Limit quick filters
✅ Use context filters
✅ Avoid too many sheets
✅ Reduce high-cardinality fields
✅ Use LOD carefully
✅ Aggregate data

---

# ✅ 22. SHARING & PUBLISHING

Options:

* Tableau Server
* Tableau Online (Cloud)
* Tableau Public (Free)
* Export → PDF / Image

User Roles:

* Viewer
* Explorer
* Creator
* Admin

---

# ✅ 23. SECURITY & GOVERNANCE

✅ Row Level Security using User Filters
✅ Project-based access
✅ Data source permission control
✅ Server authentication

---

# ✅ 24. TABLEAU + PYTHON / R / ML

✅ Python integration (TabPy)
✅ R integration (Rserve)
✅ Predictive analytics
✅ Forecasting models
✅ API integration

---

# ✅ 25. TABLEAU REAL-WORLD PROJECT WORKFLOW

1. Business Requirement Gathering
2. Data Source Identification
3. Data Cleaning (Tableau Prep / SQL)
4. Data Modeling (Relationships / Joins)
5. KPI & Calculated Fields
6. Chart Creation
7. Dashboard Design
8. Interactivity using Actions
9. User Validation
10. Publish to Server/Cloud
11. User Access Control
12. Performance Optimization
13. Maintenance & Refresh

✅ This is **REAL COMPANY BI FLOW**

---

# ✅ 26. TABLEAU VS POWER BI (INTERVIEW FAVORITE)

| Tableau                | Power BI                    |
| ---------------------- | --------------------------- |
| Best-in-class visuals  | Tight Microsoft integration |
| Strong LOD expressions | Strong DAX &                |
| modeling               |                             |
| Expensive              | Cheaper                     |
| Visualization heavy    | Reporting heavy             |

---

# ✅ 27. MOST ASKED TABLEAU INTERVIEW QUESTIONS (SHORT LIST)

* What are Dimensions & Measures?
* What are LOD expressions?
* Calculated Field vs Parameter
* Joins vs Relationships
* Extract vs Live Connection
* What are Context Filters?
* What are Dashboard Actions?
* What are Sets?
* How do you optimize Tableau performance?
* Tableau vs Power BI?

---

# ✅ 28. SHORT DEFINITIONS (RAPID RECALL)

* **LOD** → Control aggregation level
* **Extract** → In-memory data
* **Context Filter** → Primary filter
* **Parameter** → User input control
* **Set** → Custom subset
* **Hyper** → Tableau Extract Engine

---

# ✅ 29. TABLEAU + SQL (REAL JOB REQUIREMENT)

Used for:
✅ Writing complex joins
✅ Creating analytical views
✅ Performance optimization
✅ Warehouse integration

---

# ✅ 30. PERFECT COMPANY-LEVEL INTERVIEW ANSWER

If interviewer asks:
**"Explain your Tableau workflow"**

✅ Say this:

> “I start with gathering business requirements, connect to data sources like SQL or Excel, clean and shape data using Tableau Prep or SQL, create relationships and a star schema-style model, define KPIs using calculated fields and LOD expressions, build interactive visualizations and dashboards using actions and parameters, optimize performance using extracts and context filters, publish to Tableau Server or Online, manage row-level security, and iterate based on business feedback.”

✅ This answer = **Senior-Level Professional Impression**

---

# ✅ THIS NOTE IS PERFECT FOR:

✔ Data Analyst Interviews
✔ BI Developer Interviews
✔ Dashboard & Portfolio Projects
✔ Tableau Public Profiles
✔ Business Reporting Roles

---
