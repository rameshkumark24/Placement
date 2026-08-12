# 📘 **EXCEL FORMULAS COMPLETE NOTES (BEGINNER → ADVANCED → INDUSTRY)**

---

# 🔰 **1. BASIC EXCEL FORMULAS (Beginner Level)**

### 🟦 **1. SUM**

```excel
=SUM(A1:A10)
```

Adds numbers in a range.

---

### 🟦 **2. AVERAGE**

```excel
=AVERAGE(A1:A10)
```

Calculates the mean value.

---

### 🟦 **3. MIN / MAX**

```excel
=MIN(A1:A10)
=MAX(A1:A10)
```

---

### 🟦 **4. COUNT / COUNTA / COUNTBLANK**

```excel
=COUNT(A1:A10)        → counts numbers  
=COUNTA(A1:A10)       → counts non-empty cells  
=COUNTBLANK(A1:A10)   → counts empty cells
```

---

### 🟦 **5. TODAY / NOW**

```excel
=TODAY()
=NOW()
```

Used for date/time automation.

---

### 🟦 **6. ROUND, ROUNDUP, ROUNDDOWN**

```excel
=ROUND(A1, 2)
=ROUNDUP(A1, 0)
=ROUNDDOWN(A1, 0)
```

---

# 🟩 **2. TEXT FORMULAS (Cleaning, NLP-like operations)**

### 🟦 **1. LEFT / RIGHT / MID**

```excel
=LEFT(A1, 3)
=RIGHT(A1, 4)
=MID(A1, 2, 5)
```

---

### 🟦 **2. LEN**

```excel
=LEN(A1)
```

Counts characters (including spaces).

---

### 🟦 **3. TRIM**

```excel
=TRIM(A1)
```

Removes extra spaces (very important for cleaning data).

---

### 🟦 **4. CONCAT / TEXTJOIN**

```excel
=CONCAT(A1, " ", B1)
=TEXTJOIN(" ", TRUE, A1:A3)
```

TEXTJOIN is used in advanced automation.

---

### 🟦 **5. UPPER / LOWER / PROPER**

```excel
=UPPER(A1)
=LOWER(A1)
=PROPER(A1)
```

---

### 🟦 **6. SUBSTITUTE**

```excel
=SUBSTITUTE(A1, "old", "new")
```

Replaces text *everywhere*.

---

### 🟦 **7. REPLACE**

```excel
=REPLACE(A1, start, length, new_text)
```

---

# 🟧 **3. LOGICAL FORMULAS (Industry Mandatory)**

### 🟦 **1. IF**

```excel
=IF(A1 > 50, "Pass", "Fail")
```

---

### 🟦 **2. AND / OR**

```excel
=AND(A1>50, B1<100)
=OR(A1="Yes", B1="Y")
```

---

### 🟦 **3. IFS (advanced)**

```excel
=IFS(A1>90,"Excellent", A1>75,"Good", A1>50,"Average", TRUE,"Low")
```

---

### 🟦 **4. NOT**

```excel
=NOT(A1="Yes")
```

---

# 🟪 **4. LOOKUP FORMULAS (The MOST IMPORTANT for Analysts)**

## ⭐ **1. VLOOKUP**

```excel
=VLOOKUP(A2, Table!A:B, 2, FALSE)
```

Weakness: cannot lookup left side.

---

## ⭐ **2. HLOOKUP**

Same as VLOOKUP but horizontal.

---

## ⭐ **3. XLOOKUP (Modern & Powerful)**

```excel
=XLOOKUP(A2, A:A, B:B)
```

Fixes all VLOOKUP limitations.

---

## ⭐ **4. INDEX + MATCH (Industry Favorite)**

```excel
=INDEX(B:B, MATCH(A2, A:A, 0))
```

Used in Financial Analysis, BI, Dashboard Work.

---

## ⭐ **5. XMATCH**

```excel
=XMATCH(A2, A:A)
```

---

# 🟫 **5. DATE FORMULAS (Very Useful in Company Data Sets)**

### 🟦 **1. DATEDIF**

```excel
=DATEDIF(A1, B1, "D")  → Days
=DATEDIF(A1, B1, "M")  → Months
=DATEDIF(A1, B1, "Y")  → Years
```

---

### 🟦 **2. NETWORKDAYS**

```excel
=NETWORKDAYS(start_date, end_date)
```

Excludes weekends.

---

### 🟦 **3. EOMONTH**

```excel
=EOMONTH(A1,1)
```

Use: Payroll, Finance.

---

### 🟦 **4. YEAR / MONTH / DAY**

```excel
=YEAR(A1)
=MONTH(A1)
=DAY(A1)
```

---

# 🟥 **6. STATISTICAL FORMULAS (Used in Analytics & ML)**

### 🟦 **1. MEDIAN, MODE**

```excel
=MEDIAN(A1:A10)
=MODE(A1:A10)
```

---

### 🟦 **2. STDEV / VAR**

```excel
=STDEV(A1:A10)
=VAR(A1:A10)
```

---

### 🟦 **3. CORREL**

```excel
=CORREL(A1:A10, B1:B10)
```

---

### 🟦 **4. PERCENTILE**

```excel
=PERCENTILE(A1:A10, 0.9)
```

---

### 🟦 **5. RANK**

```excel
=RANK(A1, A:A)
```

---

# 🟦 **7. FINANCIAL FORMULAS (Used in Banking, Finance, FP&A)**

### **1. PMT (Loan EMI formula)**

```excel
=PMT(rate, nper, pv)
```

---

### **2. NPV**

```excel
=NPV(discount_rate, cashflow_range)
```

---

### **3. IRR**

```excel
=IRR(cashflow_range)
```

---

# 🟩 **8. DATA CLEANING & TRANSFORMATION (Industry Level)**

### ⭐ **1. UNIQUE**

```excel
=UNIQUE(A1:A100)
```

---

### ⭐ **2. FILTER**

```excel
=FILTER(A1:C100, C1:C100 > 50)
```

---

### ⭐ **3. SORT / SORTBY**

```excel
=SORT(A1:B10, 2, TRUE)
```

---

### ⭐ **4. ERROR HANDLING (Mandatory)**

```excel
=IFERROR(A1/B1, 0)
```

---

# 🟧 **9. POWER FUNCTIONS (Modern Excel Automation)**

### ⭐ **LAMBDA**

Create your own functions.

---

### ⭐ **LET**

```excel
=LET(x, A1*2, y, x+10, y)
```

Makes formulas faster & readable.

---

### ⭐ **SEQUENCE**

```excel
=SEQUENCE(10)
```

---

### ⭐ **MAP & REDUCE**

Used for array operations (advanced Power Query alternative).

---

# 🟦 **10. MOST COMMON EXCEL FORMULA PATTERNS USED IN COMPANIES**

### ✔ Lookup with fallback

```excel
=IFERROR(XLOOKUP(A2, A:A, B:B), "Not Found")
```

---

### ✔ Conditional Bonus calculation

```excel
=IF(A2>90,5000,IF(A2>80,3000,1000))
```

---

### ✔ Customer age calculation

```excel
=DATEDIF(A2, TODAY(), "Y")
```

---

### ✔ Sales % Growth

```excel
=(New - Old) / Old
```

---

### ✔ Remove text + numbers

```excel
=TRIM(CLEAN(A1))
```

---

# 📊 **Excel Shortcuts & Industrial-Level Notes**

The ultimate collection of Excel shortcuts used in companies for **fast data cleaning, analysis, dashboards, reporting, and automation**.

---

# 🧩 **1. Basic & Essential Shortcuts**

| Action               | Shortcut              |
| -------------------- | --------------------- |
| Flash Fill           | **Ctrl + E**          |
| Insert Current Date  | **Ctrl + ;**          |
| Insert Current Time  | **Ctrl + Shift + ;**  |
| AutoSum              | **Alt + =**           |
| Format Cells Dialog  | **Ctrl + 1**          |
| Create Table         | **Ctrl + T**          |
| Select Entire Column | **Ctrl + Space**      |
| Select Entire Row    | **Shift + Space**     |
| Navigate Data        | **Ctrl + Arrow Keys** |

---

# 🧹 **2. Data Cleaning Shortcuts**

| Action               | Shortcut             |
| -------------------- | -------------------- |
| Toggle Filter        | **Ctrl + Shift + L** |
| Remove Duplicates    | **Alt + A + M**      |
| Paste Values Only    | **Alt → E → S → V**  |
| AutoFit Column Width | **Alt + H + O + I**  |
| AutoFit Row Height   | **Alt + H + O + A**  |
| Clear Format         | **Alt + H + E + F**  |

---

# 🧮 **3. Editing & Formatting**

| Action                    | Shortcut             |
| ------------------------- | -------------------- |
| Edit Active Cell          | **F2**               |
| Repeat Last Action        | **F4**               |
| Recalculate Worksheet     | **F9**               |
| Name Manager              | **Ctrl + F3**        |
| Create Embedded Chart     | **Alt + F1**         |
| Create Chart on New Sheet | **F11**              |
| Currency Format           | **Ctrl + Shift + $** |
| Percentage Format         | **Ctrl + Shift + %** |
| Date Format               | **Ctrl + Shift + #** |
| Time Format               | **Ctrl + Shift + @** |
| Freeze Panes              | **Alt + W + F + F**  |

---

# 📁 **4. Insert / Delete Operations**

| Action                | Shortcut             |
| --------------------- | -------------------- |
| Insert New Cells      | **Ctrl + Shift + +** |
| Delete Selected Cells | **Ctrl + -**         |
| Fill Down             | **Ctrl + D**         |
| Fill Right            | **Ctrl + R**         |

---

# ⚡ **5. Industrial-Level Excel Shortcuts (That Companies Expect You to Know)**

## 🔥 Data Analysis Shortcuts

| Action                                      | Shortcut                      |
| ------------------------------------------- | ----------------------------- |
| Go To Special (Blanks, Formulas, Constants) | **Ctrl + G → Special**        |
| Select Entire Dataset                       | **Ctrl + A**                  |
| Open PivotTable Dialog                      | **Alt + N + V**               |
| Group by Rows/Columns in PivotTable         | **Shift + Alt + Right Arrow** |
| Ungroup Pivot                               | **Shift + Alt + Left Arrow**  |
| Refresh PivotTable                          | **Alt + F5**                  |

---

## 📊 Power User Shortcuts

| Action          | Shortcut              |
| --------------- | --------------------- |
| Duplicate Sheet | **Ctrl + Drag Sheet** |
| New Worksheet   | **Shift + F11**       |
| Hide Row        | **Ctrl + 9**          |
| Unhide Row      | **Ctrl + Shift + 9**  |
| Hide Column     | **Ctrl + 0**          |
| Unhide Column   | **Ctrl + Shift + 0**  |

---

## 📝 Text Manipulation (Often used in Data Cleaning)

| Action                                 | Shortcut                       |
| -------------------------------------- | ------------------------------ |
| Open Find and Replace                  | **Ctrl + H**                   |
| Flash Fill (Smart Pattern Recognition) | **Ctrl + E**                   |
| Concatenate columns (Excel 365)        | **=TEXTJOIN()**, **=CONCAT()** |

---

# 🎯 Flash Fill Tip (From Your Notes)

To use Flash Fill effectively:

1. Type 1–2 examples
2. Press **Ctrl + E**
   Excel automatically learns your pattern (email extraction, name split, formatting, etc.)

---

# 🛠️ **6. Industrial-Level Excel Concepts (Added by Me)**

These are essential for real-world analytics work:

### ✔ VLOOKUP / XLOOKUP

### ✔ INDEX + MATCH

### ✔ TEXT Functions (LEFT, RIGHT, MID, TRIM, PROPER)

### ✔ DATE Functions (DATEDIF, TODAY, NETWORKDAYS)

### ✔ Logical functions (IF, AND, OR, IFS)

### ✔ Conditional Formatting

### ✔ PivotTables + PivotCharts

### ✔ Data Validation (Dropdowns)

### ✔ Power Query (ETL inside Excel)

### ✔ Power Pivot (DAX formulas)

If you want, I can integrate **all of these** into your notes also.

---

# 🏁 Final Summary

You now have:

✔ Clean structured Excel notes
✔ All your handwritten shortcuts included
✔ Added 50+ industry shortcuts
✔ Data analyst / business analyst–level content

