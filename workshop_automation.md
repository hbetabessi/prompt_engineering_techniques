# Exercise — Build an AI News Digest Automation

> **Your only tool:** Any LLM of your choice (Claude, ChatGPT, Gemini…)  
> **Platform:** n8n (already running at `localhost:5678`)  
> **API:** Groq (free) — get your key at console.groq.com  
> **Goal:** A working workflow that reads Hacker News and generates a daily digest using AI

---

## What You Are Building

An automated workflow that:
1. Triggers on demand (manually)
2. Fetches the latest articles from Hacker News
3. Limits to the 5 most recent ones
4. Sends them all to an AI model **in a single API call**
5. Returns a clean, readable digest as text output

When it works, you should be able to click a button in n8n and read a summarized digest of today's top HN stories — without opening a browser.

---

## The Rules

- You must use **n8n** to build this. If you don't know n8n, ask your LLM.
- You must use **Groq API** as your AI provider. Model: `llama-3.1-8b-instant`
- The Groq API follows the OpenAI format — your LLM knows it
- You must make **exactly one call** to the Groq API — not one per article
- You are not allowed to use any pre-built n8n AI nodes — only a plain HTTP Request node for the Groq call
- You may ask your LLM anything, but you must understand every answer you use

---

## The Workflow — Step by Step

Build these nodes in order and connect them as a chain.

---

### Node 1 — Manual Trigger

**What it does:** Starts the workflow when you click a button. Nothing else.

**What to ask your LLM:**
> "How do I add a manual trigger node in n8n?"

---

### Node 2 — RSS Feed Reader

**What it does:** Fetches articles from the Hacker News RSS feed and outputs them as individual items.

**Details you need:**
- The Hacker News RSS URL is: `https://news.ycombinator.com/rss`
- Each item will have fields like `title`, `link`, `contentSnippet`
- n8n has a built-in node for this — ask your LLM which one

**What to ask your LLM:**
> "Which n8n node reads an RSS feed and how do I configure it with a URL?"

---

### Node 3 — Limit

**What it does:** Cuts the list down to only the first 5 articles. The RSS feed returns ~30 items — you don't need all of them.

**Details you need:**
- n8n has a built-in node called Limit
- You set a maximum number of items to pass through

**What to ask your LLM:**
> "How does the Limit node work in n8n? I want to keep only the first 5 items."

---

### Node 4 — Build Prompt (Code Node)

**What it does:** Takes the 5 articles and combines them into a single structured prompt that will be sent to Groq.

**This is the most important node. Read carefully.**

It must do all of this in JavaScript:
- Loop through all 5 items and extract `title`, `link`, and `contentSnippet` from each
- Format them into a numbered list like: `[1] Title \n Link: url \n snippet text`
- Write a prompt that instructs the AI to summarize them as a daily digest
- Build the **full Groq API request body** as a JSON object with these fields:
  - `model`: `"llama-3.1-8b-instant"`
  - `messages`: an array with a system message and a user message
  - `max_tokens`: 1024
  - `temperature`: 0.7
- Output one single item containing the stringified request body

**Why build the body here?** Because the next node (HTTP Request) should be completely static — no expressions, no logic. All the intelligence stays in this code node.

**What to ask your LLM:**
> "Write a JavaScript code node for n8n that takes multiple input items, extracts title, link and contentSnippet from each, formats them into a numbered list, then builds a Groq API request body with a system message and a user message containing that list. Return one item with the stringified body."

---

### Node 5 — HTTP Request (Groq API Call)

**What it does:** Sends the request body to Groq and gets back the AI-generated digest.

**Details you need:**
- Method: `POST`
- URL: `https://api.groq.com/openai/v1/chat/completions`
- You need to send an `Authorization` header with value `Bearer YOUR_GROQ_KEY`
- You need to send a `Content-Type` header with value `application/json`
- The body comes from the previous node — it's already stringified JSON
- Use the n8n **HTTP Request** node (not any AI node)

**What to ask your LLM:**
> "How do I configure an n8n HTTP Request node to POST to an API with a Bearer token header and a JSON body that comes from the previous node's output?"

---

### Node 6 — Format Output (Code Node)

**What it does:** Takes the raw Groq API response and extracts just the digest text.

**Details you need:**
- Groq returns a JSON object
- The digest text is inside: `response.choices[0].message.content`
- You should also output today's date as a title (example: `"HN Digest — Wednesday, May 7 2026"`)
- If `choices` is missing or empty, throw a readable error — don't fail silently
- Add a `tokens_used` field from `response.usage.total_tokens` so you can see the API cost

**What to ask your LLM:**
> "Write a JavaScript code node for n8n that takes a Groq API response, extracts the digest text from choices[0].message.content, formats today's date as a title, and returns a clean output object with title, digest, tokens_used and generated_at."

---

## How to See the Result

After running the workflow:
1. Click the last node (Format Output)
2. Open the **Schema** tab on the right panel
3. Find the `digest` field — that's your AI-generated summary

---

## You Are Done When

- [ ] All 6 nodes are connected and the workflow runs without errors
- [ ] The `digest` field contains readable text summarizing 5 HN articles
- [ ] You made exactly one Groq API call (check `tokens_used` — it should be a number)
- [ ] You can explain to someone what each node does and why

---

## What We Will Discuss After

- Walk us through the prompts you used to build each node
- What did the LLM get wrong on the first try? How did you fix it?
- Which node was hardest to get right and why?
- If you had to add a 7th node that emails the digest, what would you ask the LLM?

---

## Groq API — Quick Reference

```
Endpoint : https://api.groq.com/openai/v1/chat/completions
Method   : POST
Headers  : Authorization: Bearer YOUR_KEY
           Content-Type: application/json

Body structure:
{
  "model": "llama-3.1-8b-instant",
  "messages": [
    { "role": "system", "content": "..." },
    { "role": "user",   "content": "..." }
  ],
  "max_tokens": 1024,
  "temperature": 0.7
}
```

---

*The goal is not to finish first. The goal is to understand every line your LLM writes for you.*
