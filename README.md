# Open Knowledge Format (OKF) based Government Scheme Question Answering System

## Overview

This project implements an **Open Knowledge Format (OKF)** based Question Answering system for Indian Government Startup Schemes.

Instead of using a traditional Retrieval-Augmented Generation (RAG) pipeline with embeddings and vector databases, the system converts a government scheme playbook into a structured JSON knowledge base and performs deterministic retrieval before sending only the relevant information to the LLM.

The result is a lightweight, explainable, and cost-efficient architecture.

---


Every scheme generally contains:

- Scheme Name
- Ministry
- Stage
- Description
- Eligibility
- Benefits
- Funding
- Keywords

Since the information already follows a structured format, using embeddings and vector databases would introduce unnecessary complexity.

Instead, we transform every scheme into a structured JSON object (OKF), making retrieval simpler, faster, and easier to maintain.

---

# Architecture

```
                         Government Scheme PDF
                                 │
                                 ▼
                      PDF Text Extraction (PyMuPDF)
                                 │
                                 ▼
                    Split PDF into Individual Schemes
                                 │
                                 ▼
                  LangChain + Groq (Information Extraction)
                                 │
                                 ▼
                    Structured JSON (Open Knowledge Format)
                                 │
                                 ▼
                           knowledge.json
──────────────────────────────────────────────────────────────────────

                           User Question
                                 │
                                 ▼
                      Load knowledge.json
                                 │
                                 ▼
                   Create Searchable Text Index
                                 │
                                 ▼
                  Keyword / BM25 Based Retrieval
                                 │
                                 ▼
                     Top-K Relevant Schemes
                                 │
                                 ▼
                     LangChain Prompt Builder
                                 │
                                 ▼
                           Groq LLM
                                 │
                                 ▼
                    Natural Language Answer
```

---

# Project Workflow

The project consists of two major phases.

## Phase 1: Knowledge Creation (Offline)

This phase is executed only once.

### Step 1

Load the Government Scheme PDF.

### Step 2

Extract text using **PyMuPDF**.

### Step 3

Split the document into individual government schemes.

### Step 4

Use **LangChain + Groq LLM** to convert every scheme into structured JSON.

Example:

```json
{
    "name": "Startup India Seed Fund Scheme",
    "ministry": "DPIIT",
    "stage": "Seed Stage",
    "description": "...",
    "eligibility": "...",
    "benefits": "...",
    "funding": "...",
    "keywords": [
        "startup",
        "grant",
        "seed funding"
    ]
}
```

### Step 5

Store all extracted schemes inside

```
knowledge.json
```

This file becomes the permanent knowledge base.

---

# Phase 2: Question Answering

Whenever a user asks a question:

```
User Question
      │
      ▼
Load knowledge.json
      │
      ▼
Search Relevant Schemes
      │
      ▼
Select Top-K Matches
      │
      ▼
Pass Only Those Schemes to Groq
      │
      ▼
Generate Final Answer
```

Unlike RAG, the LLM never receives the entire PDF.

It only receives the most relevant structured scheme records.

---

# Search Pipeline

Every scheme is converted into a searchable string.

Example:

```
Startup India Seed Fund Scheme
DPIIT
Seed Stage
Grant
Funding
Eligibility
Startup
Innovation
```

These searchable fields are used to retrieve the most relevant schemes.

Current implementation:

- Keyword Search

Future improvement:

- BM25 Ranking

---

# Why Open Knowledge Format (OKF)?

Traditional RAG works best for unstructured documents like:

- Research papers
- Books
- Legal documents
- Medical literature

However, this project deals with structured government scheme data.

Each scheme already contains predefined fields such as:

- Name
- Ministry
- Eligibility
- Benefits
- Funding

Instead of splitting text into chunks and generating embeddings, we convert each scheme into structured knowledge objects.

Benefits include:

- No vector database required
- No embedding generation
- Lower storage requirements
- Reduced inference cost
- Easier maintenance
- Better explainability
- Faster retrieval

---

# OKF vs Traditional RAG

| Traditional RAG | Open Knowledge Format |
|-----------------|----------------------|
| Chunk PDF | Convert PDF into structured knowledge |
| Generate Embeddings | Create JSON objects |
| Vector Database | JSON Knowledge Base |
| Semantic Similarity Search | Deterministic Retrieval |
| LLM receives text chunks | LLM receives structured schemes |
| Expensive preprocessing | Lightweight preprocessing |
| Difficult to update | Easy to update individual schemes |

---

# Technologies Used

- Python
- LangChain
- Groq API
- Llama 3.3
- PyMuPDF
- JSON
- Regular Expressions
- Google Colab

---

# Knowledge Structure

Each government scheme is represented as:

```json
{
    "name": "",
    "ministry": "",
    "stage": "",
    "description": "",
    "eligibility": "",
    "benefits": "",
    "funding": "",
    "keywords": []
}
```

This structured representation forms the Open Knowledge Format.

---

# Advantages

- Structured knowledge representation
- Lightweight architecture
- Explainable retrieval
- Lower token consumption
- Faster response generation
- Easy debugging
- Easy knowledge updates
- No dependency on vector databases

--

# Conclusion

This project demonstrates an alternative to traditional Retrieval-Augmented Generation (RAG) by introducing an Open Knowledge Format (OKF) pipeline.

Instead of relying on embeddings and vector databases, the system transforms structured government documents into a machine-readable JSON knowledge base. During inference, only the most relevant knowledge objects are retrieved and provided to the LLM for reasoning.

This approach is particularly suitable for structured datasets such as government schemes, policies, regulations, catalogs, and enterprise knowledge bases, where deterministic retrieval is more effective than semantic chunk retrieval.

The resulting system is lightweight, scalable, explainable, and cost-efficient while maintaining high-quality question answering capabilities.
