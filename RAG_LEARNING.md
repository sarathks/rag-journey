# RAG Learning Journey

> A 7-step plan to go from zero to building a real RAG application.
> This file is our source of truth — updated as we learn.

---

## The Big Picture

As a UI developer, you already understand the full stack:

```
Frontend → API → Backend → Data
```

We're just extending it:

```
Angular → API → RAG → Vector DB
                 │
              LLM + Embeddings
```

Eventually:

```
Angular
  ↓ FastAPI
  ↓ LangGraph
  ↓ LangChain
  ↓ Retriever
  ↓ Vector DB
  ↓ LLM
```

No ML research. Just extending what you already know.

---

## Step 1 — LLM + RAG Fundamentals

**Status:** ✅ Done

### Concepts

**What is an LLM?**

A Large Language Model is a neural network trained on massive amounts of text. It learns patterns in language so well that it can generate new text that sounds coherent and knowledgeable.

Examples: GPT-4, Claude, Gemini, LLaMA.

It's basically a very sophisticated autocomplete. Given input text, it predicts what should come next — token by token.

---

**Prompt**

The input you send to the LLM. Everything you "say" to it is a prompt. The quality of your prompt directly affects the quality of the output. This is why "prompt engineering" became a thing.

---

**Context**

The LLM can only "see" a limited window of text at a time — this is its context window. Think of it like short-term memory. Everything the model knows about your current conversation has to fit inside this window.

This is measured in tokens.

---

**Tokens**

LLMs don't read word by word — they read in tokens. A token is roughly a word or part of a word.

- "Angular" = 1 token
- "unbelievable" = maybe 3 tokens
- GPT-4 has a ~128k token context window

Why it matters: longer context = more expensive + slower.

---

**Hallucination**

When an LLM confidently produces something that's factually wrong. It's not lying — it's pattern-matching and sometimes the pattern leads somewhere incorrect.

Example: ask it about a real person and it might invent a biography that sounds totally plausible but is wrong.

This is a fundamental problem with LLMs.

---

**Why RAG exists**

LLMs have two big limitations:
1. Their knowledge is frozen at training time (no live data)
2. You can't fit an entire knowledge base into the context window

RAG solves both. Instead of hoping the LLM "knows" the answer, you fetch the relevant information first, then give it to the LLM as context.

---

**RAG Architecture**

```
Your Documents
  ↓ chunked + embedded → stored in Vector DB

User Question
  ↓ embedded
  ↓ searched against Vector DB
  ↓ top matching chunks retrieved
  ↓ chunks + question sent to LLM as prompt
  ↓ Answer
```

The LLM isn't guessing anymore — it's reading the right docs and answering based on them.

---

## Step 2 — Embeddings

**Status:** ✅ Done

### Concepts

**What is an Embedding?**

An embedding is a way to convert text into numbers — specifically, a list of numbers called a vector. The magic is that the numbers capture *meaning*, not just characters.

So "dog" and "puppy" will have vectors that are very close to each other. "dog" and "airplane" will be far apart.

---

**Vector**

Just a list of numbers. For example:

```
"dog"      → [0.2,  0.8,  0.1,  0.9,  ...]
"puppy"    → [0.21, 0.79, 0.12, 0.88, ...]
"airplane" → [0.9,  0.1,  0.7,  0.2,  ...]
```

These vectors typically have hundreds or thousands of dimensions. You never read them manually — the math handles comparison.

---

**Semantic Similarity**

Keyword search looks for exact word matches. Semantic similarity looks for *meaning* matches.

Example:
- Query: "how do I fix a flat tire"
- Keyword search won't match: "changing a punctured tyre"
- Semantic search will — because the meaning is the same

This is what makes RAG powerful. You're not matching words, you're matching intent.

---

**Cosine Similarity**

The standard way to measure how similar two vectors are. It measures the angle between them, not the distance.

- Result of `1.0` = identical meaning
- Result of `0.0` = completely unrelated
- Result of `-1.0` = opposite meaning

You don't need to implement this yourself — every vector DB does it for you. But knowing what it means helps you understand why search results rank the way they do.

---

**Why keyword search isn't enough**

| | Keyword Search | Semantic Search |
|---|---|---|
| Matches | Exact words | Meaning |
| "car" vs "vehicle" | ❌ no match | ✅ match |
| Typos | ❌ breaks | ✅ usually fine |
| Works for RAG? | ❌ too brittle | ✅ yes |

---

### Build
```
sentence → embedding → similarity
```

---

## Step 3 — Vector DB

**Status:** ✅ Done

### Concepts

**Vector**

You already know this from Step 2 — a list of numbers representing meaning. In the context of a vector DB, each piece of text you store becomes a vector. The DB stores and indexes these vectors so it can search through millions of them fast.

---

**Collection**

A collection is like a table in a regular database — it groups related vectors together. You might have:

- a collection for your docs
- a collection for your notes
- a collection for your codebase

Each collection holds documents.

---

**Document**

A document is one piece of content you've stored — could be a paragraph, a page, a note. Each document has:
- the original text (so you can return it to the user)
- its vector (so you can search it)
- optional metadata

---

**Chunk**

Documents are often too long to embed as a whole. So you split them into smaller pieces — chunks. Each chunk gets its own embedding and is stored as its own document in the collection.

Chunk size matters a lot — we'll go deep on this in Step 6.

---

**Metadata**

Extra info you attach to a document/chunk. Things like:
- source filename
- page number
- date
- category

Metadata lets you filter search results. Example: "search only within documents tagged `angular`".

---

**Similarity Search**

When you query a vector DB, you don't write SQL. You pass in a vector (your query embedding) and it finds the stored vectors that are most similar using cosine similarity.

---

**Top-K**

The number of results you want back from a similarity search. `top_k=3` means "give me the 3 most relevant chunks". You tune this based on how much context you want to send to the LLM.

---

### Build
```
10 sentences
  ↓ embeddings
  ↓ vector DB
  ↓ search
```

---

## Step 4 — Document Processing

**Status:** 🔲 Not started

---

## Step 5 — First RAG App 🎉

**Status:** 🔲 Not started

---

## Step 6 — Improve RAG

**Status:** 🔲 Not started

---

## Step 7 — Mini Project: Ask My Notes

**Status:** 🔲 Not started

---

## Glossary

| Term | Definition |
|------|------------|
| LLM | Large Language Model — a neural network trained on massive text data that predicts and generates language token by token |
| Prompt | The input/question you send to an LLM |
| Context Window | The limited text an LLM can "see" at once — like short-term memory, measured in tokens |
| Token | The unit LLMs read in — roughly a word or part of a word |
| Hallucination | When an LLM confidently produces factually wrong output |
| RAG | Retrieval-Augmented Generation — fetch relevant docs first, then let the LLM answer based on them |
| Embedding | A list of numbers (vector) that represents the *meaning* of a piece of text |
| Vector | A list of numbers — the numerical representation of text used for similarity math |
| Cosine Similarity | A measure of the angle between two vectors — closer to 1.0 means more similar meaning |
| Semantic Similarity | Matching by meaning rather than exact words |
| Chunk | A smaller piece of a document — each chunk is embedded and stored separately |
| Collection | A group of related documents/vectors in a vector DB — like a table in SQL |
| Metadata | Extra attributes attached to a chunk (filename, page, category) used for filtering |
| Similarity Search | Querying a vector DB by passing a vector and getting back the closest matches |
| Top-K | How many top results to return from a similarity search |
