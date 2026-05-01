# Prompt Engineering Techniques

A practical, hands-on overview of the most widely used prompt engineering techniques for large language models (LLMs).  
Each technique is explained with a concise description, a use-case guide, and runnable Python examples using the OpenAI Chat Completions API.

---

## Contents

| File | Description |
|------|-------------|
| [`prompt_engineering_techniques.ipynb`](prompt_engineering_techniques.ipynb) | Main notebook — all techniques with examples |
| [`requirements.txt`](requirements.txt) | Python dependencies |

---

## Techniques Covered

| # | Technique | One-liner |
|---|-----------|-----------|
| 1 | **Zero-Shot Prompting** | Ask without examples; rely on pre-trained knowledge |
| 2 | **Few-Shot Prompting** | Provide 2–8 input/output examples to guide format and style |
| 3 | **Chain-of-Thought (CoT)** | Ask the model to reason step-by-step before answering |
| 4 | **Role Prompting** | Assign an expert persona to prime domain-specific knowledge |
| 5 | **Self-Consistency** | Sample multiple reasoning paths; pick the majority answer |
| 6 | **Prompt Chaining** | Split complex tasks into a sequence of simpler prompts |
| 7 | **Tree of Thoughts (ToT)** | Explore and evaluate multiple reasoning branches in parallel |
| 8 | **ReAct Prompting** | Interleave Reasoning and Acting (tool calls) in a loop |
| 9 | **Generated Knowledge** | Generate background facts first, then answer using them |
| 10 | **Directional Stimulus** | Add a hint or keyword to steer the model's output direction |

---

## Quick Start

```bash
# 1. Clone the repo
git clone https://github.com/hbetabessi/prompt_engineering_techniques.git
cd prompt_engineering_techniques

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your OpenAI API key
echo "OPENAI_API_KEY=sk-..." > .env

# 4. Launch the notebook
jupyter notebook prompt_engineering_techniques.ipynb
```

---

## Choosing the Right Technique

```
Simple task? ──────────────────► Zero-Shot
Specific output format? ───────► Few-Shot
Math / multi-step logic? ──────► Chain-of-Thought
Domain expertise needed? ──────► Role Prompting
High-stakes answer? ───────────► Self-Consistency
Complex pipeline? ─────────────► Prompt Chaining
Creative planning / puzzles? ──► Tree of Thoughts
Tool use / agents? ────────────► ReAct
Factual / common-sense Q&A? ───► Generated Knowledge
Guided summarisation / style? ─► Directional Stimulus
```

---

## Requirements

- Python 3.8+
- An [OpenAI API key](https://platform.openai.com/account/api-keys)
