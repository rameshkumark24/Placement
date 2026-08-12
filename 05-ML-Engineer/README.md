# 05 — ML Engineer

| File | Contents |
|---|---|
| [ML-Vibe-Coding-Cheatsheet.md](ML-Vibe-Coding-Cheatsheet.md) | **Build guide.** LLM cost/loop safety, prompt injection, data discipline, serving, monitoring, reproducibility |

## Interview material

**[Fundamentals/](Fundamentals/)** — [Data Preprocessing](Fundamentals/Data-Preprocessing.md) ·
[Scikit-Learn](Fundamentals/Scikit-Learn.md) ·
[Model Evaluation & Development](Fundamentals/Model-Evaluation-Development.md) ·
[XGBoost](Fundamentals/XGBoost.md)

**[Deep-Learning/](Deep-Learning/)** — [TensorFlow & PyTorch PrepKit](Deep-Learning/TensorFlow-PyTorch-PrepKit.md) ·
[OpenCV](Deep-Learning/OpenCV.md)

**[GenAI/](GenAI/)** — [GenAI](GenAI/GenAI.md) · [LLMs](GenAI/LLMs.md)

**[MLOps/](MLOps/)** — [MLFlow](MLOps/MLFlow.md)

**[Data-Science/](Data-Science/)** — [Statistics QA](Data-Science/Statistics-Interview-QA.md) ·
[Statistical Analysis & Visualization](Data-Science/Statistical-Analysis-Visualization.md) ·
[EDA Questions](Data-Science/EDA-Interview-Questions.md) ·
[NumPy & Pandas](Data-Science/NumPy-Pandas.md) · [Databricks](Data-Science/Databricks.md) ·
[Excel](Data-Science/Excel.md) · [Power BI](Data-Science/PowerBI.md) · [Tableau](Data-Science/Tableau.md) ·
[Capgemini Data Analytics](Data-Science/Capgemini-Data-Analytics-Questions.md)

> Universal build rules live in [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/).
> Serving stack overlaps with [`01-Python-Developer`](../01-Python-Developer/).
> Docker/MLOps notes: [`06-Common/Cloud-DevOps/Docker`](../06-Common/Cloud-DevOps/Docker/).

## The three that cause the most damage

1. **An unbounded agent loop** → a tool calls the agent, which calls the tool. Set `MAX_STEPS`.
2. **RAG retrieval without a permission filter** → user A's query surfaces user B's documents.
   A cross-tenant data leak with extra steps.
3. **Loading the model inside the request handler** → 3 seconds of latency on every single call.

All three: [ML-Vibe-Coding-Cheatsheet.md](ML-Vibe-Coding-Cheatsheet.md)
