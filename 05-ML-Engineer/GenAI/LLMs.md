# 🧠 COMPLETE LLM CHEATSHEET  
### Beginner → Pro → Architect Level  
Think of this as your **LLM Bible.** Save it. Revisit it. Build with it.

---

## 0️⃣ LLM Mental Model (Before Anything Else)

LLMs are not “intelligent.”  
They are **probability machines** trained to predict the next token given context.

**Core idea:**  
Text → Tokens → Numbers → Transformer → Probabilities → Text

**LLMs:**
- Do **NOT** think  
- Do **NOT** reason like humans  
- Do **NOT** understand truth  
- They **model language patterns** extremely well  

---

## 1️⃣ Foundations (Beginner – Must Know)

### 🔹 What is an LLM?
A **Large Language Model** trained on massive text data  
Learns **statistical relationships** between tokens  
Outputs the **most probable next token sequence**

### 🔹 Tokens
Text is split into tokens (not characters, not words)  
Example:  
`"ChatGPT is powerful"` → `["Chat", "G", "PT", " is", " powerful"]`

**Tokenization libraries:**
- BPE (Byte Pair Encoding)  
- WordPiece  
- SentencePiece  

### 🔹 Parameters
Weights inside the neural network  
Examples:
- 7B = 7 billion parameters  
- 70B = 70 billion parameters  

_⚠️ More parameters ≠ always better (cost, latency trade-offs)._

---

## 2️⃣ Transformer Architecture (Core – Non-Negotiable)

**Why Transformers?**
- RNN / LSTM → Slow, sequential, forget long context  
- Transformers → Parallel, long-range, scalable  

**5 Core Blocks:**
1. **Embedding Layer** – Converts tokens → vectors  
2. **Positional Encoding** – Adds word order info  
3. **Self-Attention** – Each token attends to others  
4. **Feed Forward Network (FFN)** – Non-linear transformation  
5. **Layer Norm + Residuals** – Stability & gradient flow  

---

## 3️⃣ Attention (Deep Understanding Required)

**Formula:**  
`Attention(Q, K, V) = softmax(QKᵀ / √d) * V`

Where:  
Q = Query | K = Key | V = Value  

**Multi-Head Attention:**  
Runs multiple attention heads in parallel — each focuses on syntax, semantics, or long-distance meaning.

---

## 4️⃣ Training Pipeline (Beginner → Intermediate)

**Pretraining (Unsupervised):**
- Task: Next Token Prediction  
- Data: Internet text, books, code  
- Loss: Cross-Entropy  

**Fine-Tuning (Supervised):**
- Task: Instruction → Desired Output  
- Makes model usable for tasks  

**Alignment:**
- RLHF (Reinforcement Learning from Human Feedback)  
- Reduces toxicity, improves helpfulness  

---

## 5️⃣ Model Types (Must Differentiate)

| Type | Examples | Typical Use |
|------|-----------|--------------|
| **Decoder-Only** | GPT, LLaMA, Mistral | Chat, Code, Completion |
| **Encoder-Decoder** | T5, BART | Translation, Summarization |
| **Encoder-Only** | BERT | Classification, Embeddings |

---

## 6️⃣ Inference Basics (Very Important)

- **Temperature:** randomness (0.0 deterministic → 1.0 creative)  
- **Top-K:** choose from top K probable tokens  
- **Top-P:** choose tokens with cumulative probability ≤ P  
- **Max Tokens:** output length cap  

---

## 7️⃣ Prompt Engineering (Intermediate)

**Prompt Structure:**  
_System → Instructions → Context → Examples → Question_

**Techniques:**  
- Zero-shot / Few-shot  
- Chain-of-Thought  
- Role prompting  
- Delimiters (```)  

**Avoid:**  
❌ Vague prompts  
❌ No constraints  
❌ Asking model to “know” private data  

---

## 8️⃣ Embeddings (Critical for Real Apps)

**Definition:** Dense vector representing meaning.  
Similar meaning → similar vectors.

**Uses:**  
- Semantic search  
- Clustering  
- Recommendation  
- RAG  

**Popular Vector DBs:** FAISS, Chroma, Pinecone, Weaviate  

---

## 9️⃣ RAG (Retrieval-Augmented Generation)

**Why:**  
LLMs hallucinate and have outdated data.

**Process:**  
Query → Retrieve Docs → Inject Context → Generate Answer  

**RAG Stack:**  
Loader → Chunking → Embedding → Vector Search → Prompt Injection  

---

## 🔟 Fine-Tuning (Advanced)

**Full Fine-Tuning:** Expensive, risk of forgetting  
**PEFT (Preferred):** LoRA, QLoRA, Adapters  

**When to Fine-Tune?**  
✅ Style / domain language change  
❌ Factual updates (use RAG instead)  

---

## 1️⃣1️⃣ Evaluation (Senior Level)

**Automatic Metrics:** Perplexity, BLEU, ROUGE  
**Human Evaluation:** Accuracy, Helpfulness, Hallucination Rate  
**LLM-as-Judge:** One model grades another  

---

## 1️⃣2️⃣ Hallucinations

**Causes:**  
- Probability ≠ truth  
- Missing context  
- Overconfidence  

**Fixes:**  
- RAG  
- Lower temperature  
- Cite sources  
- Answer “unknown” when unsure  

---

## 1️⃣3️⃣ Security & Safety

- **Prompt Injection** → user overrides system  
- **Jailbreaking** → manipulate model  
- **Data Leakage** → expose sensitive data  

**Mitigations:**  
Input sanitization, system prompt locking, output filtering  

---

## 1️⃣4️⃣ Open-Source LLM Ecosystem

**Models:** LLaMA, Mistral, Falcon, DeepSeek, Gemma  
**Tools:** Hugging Face, LangChain, LlamaIndex, Ollama, vLLM  

---

## 1️⃣5️⃣ Deployment (Real World)

- **Serving:** CPU vs GPU, Quantization (INT8, INT4), Batching  
- **Latency Optimization:** KV caching, smaller context, streaming  

---

## 1️⃣6️⃣ Architect-Level Decisions (Expert)

Questions to ask:
- RAG or Fine-Tune?  
- Open-source or API?  
- Latency vs Accuracy?  
- Cost per 1K tokens?  
- Privacy constraints?  

---

## 1️⃣7️⃣ Senior LLM Checklist ✅

| Level | Key Competencies |
|--------|------------------|
| **Beginner** | Tokens, Transformers, Attention, Prompt basics |
| **Intermediate** | RAG, Embeddings, Sampling, Vector DB |
| **Advanced** | LoRA tuning, Evaluation, Security |
| **Professional** | Deployment, Scaling, Monitoring, Cost-efficiency |

---

## 1️⃣8️⃣ Final Truth

LLMs are **tools**, not **magic.**  
Real value comes from **system design**, not raw generation.  
If you’re thinking like a **builder**, you’re already on the **senior track.**

---

# 🧠 LLM INTERVIEW QUESTIONS (Industry Based)

---

## 🟢 Beginner Level (Entry / Intern)

1️⃣ **What is an LLM?**  
A neural network trained on large text data to predict the next token probabilistically.

2️⃣ **Difference from traditional ML models?**  
Traditional = task-specific  
LLM = general-purpose, pre-trained  

3️⃣ **What is tokenization?**  
Breaking text into tokens. Token count affects latency & cost.

4️⃣ **Why transformers over RNNs/LSTMs?**  
Parallelism, long-range dependency, attention mechanism.

5️⃣ **What is attention in simple terms?**  
Mechanism deciding which words matter most for predicting the next word.

6️⃣ **What does temperature control?**  
Randomness → creativity vs determinism.

7️⃣ **What is fine-tuning?**  
Behavior/style adaptation — **not** factual memory addition.

---

## 🟡 Intermediate Level (Junior AI Engineer / GenAI Developer)

8️⃣ **Explain self-attention.**  
Uses Query–Key–Value relationships to capture word dependencies (e.g., “bank” = river vs money).

9️⃣ **Difference: Top-K vs Top-P**

| Top-K | Top-P |
|--------|--------|
| Fixed number | Probability threshold |
| Less flexible | More natural output |

🔟 **Why do LLMs hallucinate?**  
Because they predict next tokens, not truth — often missing context.

1️⃣1️⃣ **What are embeddings used for?**  
Semantic search, recommendation, RAG retrieval.

1️⃣2️⃣ **What is RAG?**  
Combines retrieval + generation to handle outdated data or hallucinations.

1️⃣3️⃣ **Why chunking in RAG?**  
Respects context window limits, improves retrieval accuracy.

1️⃣4️⃣ **Does RAG eliminate hallucinations?**  
No — but it greatly **reduces** them.

1️⃣5️⃣ **Prompt engineering vs Fine-tuning?**  
Prompting changes **instructions**; fine-tuning changes **model weights.**

---

## 🟠 Advanced Level (LLM Engineer / Applied Scientist)

1️⃣6️⃣ **RAG vs Fine-Tuning:**  
Use **RAG** for knowledge updates; **Fine-tuning** for style/behavioral changes.

1️⃣7️⃣ **LoRA / QLoRA:**  
Freeze base model → train small adapter matrices → efficient fine-tuning.

1️⃣8️⃣ **Evaluate LLM outputs:**  
Automatic (BLEU/ROUGE), Human eval, or LLM-as-judge.

1️⃣9️⃣ **Prompt Injection:**  
Input override that changes system instructions.

2️⃣0️⃣ **Prevent Prompt Injection:**  
Prompt isolation, sanitization, validation steps.

2️⃣1️⃣ **Context window limitation:**  
Models process limited tokens — affects memory, cost.

2️⃣2️⃣ **KV caching:**  
Remembers previous attention keys/values → speeds up inference.

---

## 🔴 Senior / Architect Level

2️⃣3️⃣ **Design a customer-support LLM system:**  
RAG + Vector DB + Safety layer + Logging + Feedback loop.

2️⃣4️⃣ **Open-source vs API decision:**  
Compare cost, latency, privacy, and customization.

2️⃣5️⃣ **Reduce LLM cost in production:**  
Smaller models, caching, prompt optimization, quantization.

2️⃣6️⃣ **Monitor LLM performance:**  
Hallucination rate, latency, token usage, user feedback.

2️⃣7️⃣ **Vector DB returns irrelevant docs — what now?**  
Re-ranking, improved chunking, or hybrid search.

2️⃣8️⃣ **Secure enterprise LLM system:**  
PII masking, access control, audit logs, prompt control.

2️⃣9️⃣ **Model upgrades without behavior breakage:**  
Versioned prompts, A/B tests, regression eval.

3️⃣0️⃣ **Hallucination vs misinformation:**  
Hallucination → fabricated content  
Misinformation → incorrect learned data  

---

## 🏁 Final Tip  

Interviewers want **reasoning**, **trade-offs**, and **real-world systems**,  
not textbook definitions.  
Show how you think — that’s what gets you hired.
