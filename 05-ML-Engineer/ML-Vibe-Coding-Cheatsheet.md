# 🤖 ML Engineer — Vibe-Coding Cheatsheet

Shipping ML and LLM features as production systems, built with an AI agent.

> The universal rules live in [`03-Web-Developer`](../03-Web-Developer/) phases 04–08 — especially
> [05-API-Safety.md](../03-Web-Developer/05-API-Safety.md), because LLM features are where runaway
> loops become expensive fastest.
>
> Interview material: [`Fundamentals/`](Fundamentals/) · [`Deep-Learning/`](Deep-Learning/) ·
> [`GenAI/`](GenAI/) · [`MLOps/`](MLOps/) · [`Data-Science/`](Data-Science/)

---

## The one thing that separates ML demos from ML products

A notebook that reaches 94% accuracy is a demo. A product needs: a versioned model, a reproducible
training run, a serving path with a timeout, a monitored input distribution, and a rollback plan.
The gap between those two is where most ML projects die — and an AI agent will happily help you
build the demo and stop there.

---

## 1. Stack

| Need | Default | Notes |
|---|---|---|
| Serving | **FastAPI** | Same stack as [Python Developer](../01-Python-Developer/) |
| Experiment tracking | **MLflow** | See [MLOps/MLFlow.md](MLOps/MLFlow.md) |
| Data/model versioning | DVC or LakeFS | Git can't hold a 2GB checkpoint |
| Pipelines | Prefect / Dagster | Airflow if it's already there |
| Feature store | Feast | Only when training/serving skew is real |
| Vector DB | pgvector → Qdrant | Start with pgvector; you already have Postgres |
| LLM orchestration | Direct SDK calls | LangChain adds indirection agents get lost in |
| Evaluation | Ragas, DeepEval, promptfoo | |
| Monitoring | Evidently (drift) + Sentry | |
| Serving runtime | TorchServe / BentoML / ONNX Runtime | ONNX for CPU-only cost savings |
| Containers | Docker | [Docker notes](../06-Common/Cloud-DevOps/Docker/Docker-Kubernetes-MLOps.md) |

---

## 2. LLM app safety — read this before wiring any model call

This is where a vibe-coded ML feature turns into a five-figure bill overnight.

### Bounded agent loops

```python
MAX_STEPS = 10
MAX_TOKENS_PER_REQUEST = 4000

def run_agent(messages: list, tools: list) -> str:
    for step in range(MAX_STEPS):
        result = client.messages.create(
            model="claude-sonnet-5",
            messages=messages,
            tools=tools,
            max_tokens=MAX_TOKENS_PER_REQUEST,
        )
        if result.stop_reason != "tool_use":
            return result.content[0].text
        messages.append(run_tool(result))
    raise RuntimeError(f"Agent did not converge in {MAX_STEPS} steps")
```

**Never let the model decide its own iteration count.** A tool that calls the agent, which calls
the tool, is an unbounded spend loop with no natural stopping point.

### The LLM cost checklist

- [ ] Hard spend cap set in the provider console **on day one** (Anthropic/OpenAI usage limits)
- [ ] Alerts at 50% and 80% of the cap
- [ ] Per-user daily token quota enforced in **your** code — the provider cap protects your wallet,
      not fairness between users
- [ ] `MAX_STEPS` constant on every agent loop
- [ ] `max_tokens` set on every call — never unbounded
- [ ] Timeout on the whole agent run, not just individual calls
- [ ] Streaming cancelled when the client disconnects (otherwise you pay for unread tokens)
- [ ] **Prompt caching** enabled for long, stable system prompts — often a 5–10× cost reduction
- [ ] Identical prompts cached (hash the input, store the output)
- [ ] Cheaper model routed to for simple tasks; the biggest model isn't always needed
- [ ] Retrieval limited to top-k with a token budget — don't stuff 50 chunks into context
- [ ] Separate API keys for dev and prod, dev on a low limit
- [ ] Cost-per-request logged so you can see drift

### Prompt injection & output handling

- [ ] User content placed in a clearly delimited block; the system prompt states that content
      within it is **data, not instructions**
- [ ] Model output **never** executed — no `eval`, no `exec`, no shell, no SQL interpolation
- [ ] Output escaped before rendering (models emit XSS payloads happily)
- [ ] Tool calls validated against a schema before execution — never trust the arguments
- [ ] Tools that write or delete require a confirmation step or are read-only in v1
- [ ] The model never receives secrets, other users' data, or full DB dumps in context
- [ ] RAG retrieval **filtered by the requesting user's permissions** — otherwise your vector
      search is a cross-tenant data leak with extra steps
- [ ] PII not sent to third-party models without consent and a DPA

> **The RAG IDOR:** embedding every document into one shared index and retrieving by similarity
> alone means user A's query can surface user B's document. Filter by `user_id`/`tenant_id`
> *in the vector query*, not after retrieval.

---

## 3. Data & training discipline

- [ ] Data source licence read; redistribution rights confirmed **in writing**
- [ ] Train/val/test split done **before** any exploration — otherwise you leak through your own
      decisions
- [ ] Split is stratified, and grouped by entity where rows share a subject
- [ ] **Time-based split for anything temporal** — random splits on time series leak the future
- [ ] Preprocessing fitted on train only, then applied to val/test (`Pipeline`, not manual steps)
- [ ] No target-derived feature in the input set (the classic leak)
- [ ] Class imbalance handled deliberately, and accuracy not used as the metric when it's imbalanced
- [ ] Every run logged to MLflow: params, metrics, data version, git SHA, environment
- [ ] Random seeds fixed and recorded
- [ ] A baseline exists (majority class / simple heuristic) — if the model can't beat it, stop
- [ ] Metric chosen to match the business cost of each error type, not by default

**The leak test:** if your model is suspiciously good, assume leakage before celebrating. Drop the
most predictive feature and re-run — a huge drop usually means that feature encodes the target.

---

## 4. Serving

- [ ] Model loaded **once at startup**, not per request
      (agents write `model = load()` inside the handler — that's a 3-second-per-request bug)
- [ ] Inference has a timeout and a max input size
- [ ] Batch endpoint has a server-enforced max batch size
- [ ] Input validated with Pydantic before it reaches the model
- [ ] Preprocessing code **shared** between training and serving — duplicated logic is how
      training/serving skew starts
- [ ] Model version returned in the response, and logged
- [ ] Graceful fallback when inference fails (cached prediction, heuristic, or an honest error)
- [ ] Heavy inference off the request path — queue it, return a job ID
- [ ] GPU memory limits set; concurrent request limit enforced
- [ ] `/health` checks the model is actually loaded, not just that the process is alive
- [ ] Rate limiting per user — inference is expensive, so this is a cost control, not just abuse control

```python
# ✅ Load once at startup
@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.model = joblib.load("model.pkl")     # once
    yield
    app.state.model = None

app = FastAPI(lifespan=lifespan)
```

---

## 5. Monitoring — what ML needs beyond normal observability

A normal service fails loudly. An ML service fails **silently and correctly-looking**.

- [ ] Prediction distribution tracked over time
- [ ] Input feature drift monitored (Evidently) with an alert threshold
- [ ] Ground truth collected where possible, and accuracy tracked in production
- [ ] Every prediction logged with its inputs, model version, and latency
- [ ] p95 inference latency alerted, not the average
- [ ] Fallback rate tracked — a rising fallback rate is the early warning
- [ ] For LLMs: token usage, cost per user, refusal rate, eval scores on a fixed golden set
- [ ] Retraining trigger defined — on a schedule, or on a drift threshold, written down
- [ ] Rollback to the previous model version tested at least once

---

## 6. Reproducibility

- [ ] `requirements.txt` / `uv.lock` pinned exactly
- [ ] Random seeds fixed (`numpy`, `torch`, `random`, and the framework's own)
- [ ] Data version recorded with the run (DVC hash or a snapshot ID)
- [ ] Git SHA logged with every experiment
- [ ] Training runs in a container, not on your laptop's ambient environment
- [ ] CUDA/driver versions recorded
- [ ] The whole pipeline runs end to end from a clean checkout — verify this, don't assume it
- [ ] Model artefacts versioned in a registry, never a file called `model_final_v2_REAL.pkl`

---

## 7. Notebook → production

Notebooks are for exploration. They are not the deliverable.

- [ ] Logic extracted into importable modules under `src/`
- [ ] Notebook outputs stripped before commit (`nbstripout`)
- [ ] No credentials in a notebook — ever, not even briefly
- [ ] Hidden-state bugs eliminated: **Restart & Run All** must pass before you trust a result
- [ ] Hardcoded paths replaced with config
- [ ] `print()` replaced with logging
- [ ] Tests written for the extracted functions

---

## Drop into `CLAUDE.md`

```md
## ML rules
- Load the model once at application startup, never inside a request handler.
- Preprocessing is shared between training and serving. Never duplicate the logic.
- Every LLM/agent loop has an explicit MAX_STEPS constant and max_tokens set.
- Never execute model output. Never interpolate it into SQL or a shell command.
- RAG retrieval always filters by the requesting user's id in the vector query.
- Every experiment logs params, metrics, data version and git SHA to MLflow.
- Fit preprocessing on train only. Never fit on the full dataset.
- Use a time-based split for temporal data.
- Do not add a new model or dataset without telling me the licence.
```
