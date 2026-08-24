# 05 — ML Engineer

> ⭐⭐ **GenAI and LLM engineering are no longer here.** They are Stage 13/14 of the Python track —
> **[Days 414–461](../01-Python-Developer/Days/)**, 48 written days covering the model, prompting,
> evals, retrieval, agents, safety and production. The old `GenAI/` notes and the ML vibe-coding
> cheatsheet were folded into those days and removed, so there is one source of truth.
>
> This folder is now what those days deliberately do **not** cover: **training models** rather than
> using them — classical ML, deep learning, data science and MLOps.

## Interview material

**[Fundamentals/](Fundamentals/)** — [Data Preprocessing](Fundamentals/Data-Preprocessing.md) ·
[Scikit-Learn](Fundamentals/Scikit-Learn.md) ·
[Model Evaluation & Development](Fundamentals/Model-Evaluation-Development.md) ·
[XGBoost](Fundamentals/XGBoost.md)

**[Deep-Learning/](Deep-Learning/)** — [TensorFlow & PyTorch PrepKit](Deep-Learning/TensorFlow-PyTorch-PrepKit.md) ·
[OpenCV](Deep-Learning/OpenCV.md)

**[MLOps/](MLOps/)** — [MLFlow](MLOps/MLFlow.md)

**[Data-Science/](Data-Science/)** — [Statistics QA](Data-Science/Statistics-Interview-QA.md) ·
[Statistical Analysis & Visualization](Data-Science/Statistical-Analysis-Visualization.md) ·
[EDA Questions](Data-Science/EDA-Interview-Questions.md) ·
[NumPy & Pandas](Data-Science/NumPy-Pandas.md) · [Databricks](Data-Science/Databricks.md) ·
[Excel](Data-Science/Excel.md) · [Power BI](Data-Science/PowerBI.md) · [Tableau](Data-Science/Tableau.md) ·
[Capgemini Data Analytics](Data-Science/Capgemini-Data-Analytics-Questions.md)

> Universal build/security/API rules live in [`03-Web-Developer`](../03-Web-Developer/) phases 04–08.
> Serving stack overlaps with [`01-Python-Developer`](../01-Python-Developer/).
> Docker/MLOps notes: [`06-Common/Cloud-DevOps/Docker`](../06-Common/Cloud-DevOps/Docker/).

## Where the old cheatsheet's three warnings went

The three failures that used to head this file are now full lessons, with the mechanism and the fix:

1. **An unbounded agent loop** → ⭐⭐ [Day 439](../01-Python-Developer/Days/Day-439.md) — `MAX_STEPS`
   as a *correctness* boundary, not a safety net, plus the four budgets an agent run needs.
2. **RAG retrieval without a permission filter** → ⭐⭐
   [Day 434](../01-Python-Developer/Days/Day-434.md) — why the leak is *silent*: generation launders
   another tenant's data into fluent prose, so no error rate, latency or row count moves.
3. **Loading the model inside the request handler** →
   [Day 453](../01-Python-Developer/Days/Day-453.md) — and the wider point, that one synchronous call
   in an async handler blocks the event loop for every other request.
