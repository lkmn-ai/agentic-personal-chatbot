# 🤖 Agentic AI Personal Chatbot
### Evaluator-Optimizer Workflow · Ollama · Qwen 2.5 · Phi-3 Mini · Gradio

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Ollama-Local%20LLM-black?style=for-the-badge&logo=ollama&logoColor=white"/>
  <img src="https://img.shields.io/badge/Qwen%202.5-Chat%20Model-orange?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Phi--3%20Mini-Evaluator-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Gradio-UI-FF6B6B?style=for-the-badge&logo=gradio&logoColor=white"/>
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge"/>
</p>

---

## 📌 Overview

This project is a **fully local, agentic AI chatbot** that represents a professional persona on a personal website. It goes beyond a simple LLM wrapper by implementing a **multi-agent Evaluator-Optimizer workflow** — where one AI model generates responses and a second AI model evaluates and enforces quality, automatically triggering a retry loop if the response doesn't meet the bar.

Built entirely with **open-source local models** via [Ollama](https://ollama.com/) — no OpenAI API key, no cloud costs, full privacy.

---

## 🧠 Architecture — Evaluator-Optimizer Agentic Workflow

```
User Message
     │
     ▼
┌─────────────────────────────┐
│       CHAT AGENT            │
│   Model: qwen2.5:latest     │
│  - Reads LinkedIn PDF       │
│  - Reads personal summary   │
│  - Dynamic prompt injection │
│    (GenAI topic detection)  │
└────────────┬────────────────┘
             │ Draft reply
             ▼
┌─────────────────────────────┐
│     EVALUATOR AGENT         │
│     Model: phi3:mini        │
│  - Scores reply quality     │
│  - Returns structured JSON  │
│    { is_acceptable, feedback}│
└────────────┬────────────────┘
             │
     ┌───────┴────────┐
     │                │
  PASS ✅          FAIL ❌
     │                │
     │         ┌──────▼──────────────┐
     │         │   OPTIMIZER / RERUN │
     │         │  Model: qwen2.5     │
     │         │  - Receives feedback│
     │         │  - Regenerates reply│
     │         └──────┬──────────────┘
     │                │
     └───────┬────────┘
             ▼
     Final Response → Gradio UI → User
```

### Key Agentic Behaviours

| Behaviour | Implementation |
|---|---|
| **Self-evaluation** | Phi-3 Mini scores every reply before it reaches the user |
| **Automatic retry** | Failed replies are regenerated with rejection feedback injected into the prompt |
| **Dynamic context switching** | System prompt is enriched when Generative AI topics are detected |
| **Structured output** | Evaluator returns a Pydantic-validated `Evaluation(is_acceptable, feedback)` object |
| **Grounded persona** | Agent is grounded with a real LinkedIn PDF + personal summary at every turn |

---

## ✨ Features

- 🏠 **Fully local** — runs on your machine via Ollama, zero API costs
- 🔁 **Evaluator-Optimizer loop** — two-model agentic pipeline with automatic quality control
- 📄 **PDF grounding** — LinkedIn profile parsed and injected into every conversation
- 🧬 **Dynamic prompt injection** — detects Generative AI queries and switches to expert mode
- 💬 **Gradio web UI** — clean chat interface with suggested questions and avatar support
- 🔒 **Privacy first** — no data ever leaves your machine

---

## 🗂️ Project Structure

```
├── app.py                  # Main Gradio app — UI + agentic workflow
├── me/
│   ├── linkedin.pdf        # Your LinkedIn profile (PDF export)
│   └── summary.txt         # Short personal/professional summary
├── requirements.txt        # Python dependencies
└── README.md
```

---

## 🚀 Quick Start

### 1. Prerequisites

- Python 3.10+
- [Ollama](https://ollama.com/download) installed and running

### 2. Pull the models

```bash
ollama pull qwen2.5
ollama pull phi3:mini
```

### 3. Clone the repo

```bash
git clone https://github.com/lkmn-ai/agentic-ai-chatbot.git
cd agentic-ai-chatbot
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

### 5. Add your personal context

```
me/
├── linkedin.pdf     ← Export your LinkedIn profile as PDF
└── summary.txt      ← Write 1–2 paragraphs about yourself
```

### 6. Run

```bash
python app.py
```

Open **http://localhost:7860** in your browser.

---

## 📦 Requirements

```txt
openai
gradio
pypdf
pydantic
```

Install all at once:

```bash
pip install openai gradio pypdf pydantic
```

---

## 🔧 Configuration

All configuration is at the top of `app.py`:

```python
name    = "Your Name"          # Your name — used throughout prompts
model   = "qwen2.5:latest"     # Chat / optimizer model
eval_model = "phi3:mini"       # Evaluator model
port    = 7860                 # Gradio server port
```

To switch models, just swap the Ollama model strings — any model available via `ollama list` works.

---

## 🤖 How the Evaluator-Optimizer Works

This project implements a classic **Evaluator-Optimizer** agentic pattern:

1. **Generator** (`qwen2.5`) produces an initial reply grounded in LinkedIn and summary context
2. **Evaluator** (`phi3:mini`) critiques the reply using a Pydantic structured output schema:
   ```python
   class Evaluation(BaseModel):
       is_acceptable: bool
       feedback: str
   ```
3. If `is_acceptable=False`, the **Optimizer** re-prompts the generator with the rejection reason injected directly into the system prompt
4. The final response — either first-pass or optimized — is returned to the user

This pattern ensures every response meets a quality bar before the user ever sees it.

---

## 🧩 Agentic Design Patterns Used

| Pattern | Description |
|---|---|
| **Evaluator-Optimizer** | Generator + Evaluator + conditional retry loop |
| **Structured Output** | Pydantic schema enforces evaluator response format |
| **Dynamic Prompt Injection** | Context-aware system prompt switching at runtime |
| **RAG-lite** | PDF + text file grounding injected at every conversation turn |
| **Persona Grounding** | Agent instructed to faithfully represent a real professional identity |

---

## 📸 UI Preview

```
┌────────────────────────────────────────────────┐
│  💬 Chat with Lakshmikanth                      │
│  Powered by Qwen 2.5 + Phi-3 Mini via Ollama   │
├────────────────────────────────────────────────┤
│                                                │
│  [Bot]: Hi! I'm Lakshmikanth. Ask me about    │
│         my experience in cloud infrastructure  │
│         and AI projects...                     │
│                                                │
│  [You]: What Generative AI skills do you have? │
│                                                │
│  [Bot]: Great question! I've been working      │
│         with LLMs, RAG pipelines, and ...      │
│                                                │
├────────────────────────────────────────────────┤
│  [ Type your message...          ] [ Send ➤ ]  │
├────────────────────────────────────────────────┤
│  💡 Tell me about your background              │
│     What are your key technical skills?        │
│     What experience with Generative AI?        │
│     What kind of roles are you open to?        │
└────────────────────────────────────────────────┘
```

---

## 🛠️ Extending the Project

- **Swap models** — try `llama3`, `mistral`, `gemma2` for the chat agent
- **Add voice** — pipe responses through a TTS model (e.g. `kokoro` via Ollama)
- **Add memory** — persist conversation history to SQLite for long-term context
- **Deploy** — containerise with Docker and serve behind Nginx for a production site
- **Multi-persona** — load different PDFs/summaries to represent different people

---

## 📄 License

MIT License — free to use, fork, and build on.

---

## 🙋 Author

**Lakshmikanth MN**
[![GitHub](https://img.shields.io/badge/GitHub-lkmn--ai-181717?style=flat&logo=github)](https://github.com/lkmn-ai)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/lakshmikanth-mn)

---

> *"The best AI systems don't just generate — they evaluate, learn, and improve."*
